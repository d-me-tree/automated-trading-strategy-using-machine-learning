# reference: https://hub.docker.com/_/ubuntu/
FROM ubuntu:20.04
LABEL maintainer="Stefan Jansen <stefan@applied-ai.com>"
LABEL version="1.0"

USER root

# Install all OS dependencies for notebook server that starts but lacks all
# features (e.g., download as all possible file formats)
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update --fix-missing > /dev/null && \
    apt-get -qq install --no-install-recommends \
        apt-utils \
        build-essential \
        bzip2 \
        ca-certificates \
        fonts-liberation \
        gcc \
        git \
        libbz2-dev \
        libc6-dev \
        libgdbm-dev \
        libglib2.0-0 \
        libncursesw5-dev \
        libreadline-gplv2-dev \
        libsm6 \
        libsqlite3-dev \
        libssl-dev \
        libxext6 \
        libxrender1 \
        locales \
        nodejs \
        npm \
        python3-dev \
        run-one \
        sudo \
        tk-dev \
        wget >/dev/null && \
    apt-get clean -y && rm -rf /var/lib/apt/lists/* && \
    echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && locale-gen

# create a new user (defaults to manning)
ARG USER="manning"
ARG UID=1000
ARG GID=100
ENV USER=$USER
ENV UID=$UID
ENV GID=$GID
ENV HOME=/home/$USER

# Configure environment
ENV CONDA_DIR=/opt/conda
ENV CONDA_ENVS_PATH=/opt/conda/envs
ENV SHELL=/bin/bash
ENV LC_ALL=en_US.UTF-8
ENV LANG=en_US.UTF-8
ENV LANGUAGE=en_US.UTF-8

ENV PATH=$CONDA_DIR/bin:$PATH

# Copy a script that we will use to correct permissions after running certain commands
COPY fix-permissions /usr/local/bin/fix-permissions
RUN chmod a+rx /usr/local/bin/fix-permissions

# Enable prompt color in the skeleton .bashrc before creating the default USER
# Create USER with name manning with UID=1000 in the 'users' group
# and make sure these dirs are writable by the `users` group.
RUN sed -i 's/^#force_color_prompt=yes/force_color_prompt=yes/' /etc/skel/.bashrc && \
    chmod a+rx /usr/local/bin/fix-permissions && \
    echo "auth requisite pam_deny.so" >> /etc/pam.d/su && \
    sed -i.bak -e 's/^%admin/#%admin/' /etc/sudoers && \
    sed -i.bak -e 's/^%sudo/#%sudo/' /etc/sudoers && \
    groupadd -g $GID -o $USER && \
    useradd -m -u $UID -g $GID -o -s /bin/bash $USER && \
    mkdir -p $CONDA_DIR && \
    mkdir -p $HOME/liveproject && \
    chown $USER:$GID $CONDA_DIR && \
    chmod g+w /etc/passwd

USER $USER
WORKDIR $HOME

# Install conda as user 'manning' and check the md5 sum provided on the download site
ARG PYTHON_VERSION=3.7
ENV MINICONDA_VERSION=4.8.2
ENV MINICONDA_SHA256=957d2f0f0701c3d1335e3b39f235d197837ad69a944fa6f5d8ad2c686b69df3b
ENV CONDA_VERSION=4.8.3
ENV TMP_FILE="$HOME/miniconda.sh"


COPY --chown=$USER *.yml $HOME/tmp/

RUN cd /tmp && \
    wget -q https://repo.continuum.io/miniconda/Miniconda3-py37_${MINICONDA_VERSION}-Linux-x86_64.sh -O $TMP_FILE && \
    echo "${MINICONDA_SHA256} ${TMP_FILE}" | sha256sum -c - && \
    /bin/bash $TMP_FILE -f -b -p $CONDA_DIR && \
    rm $TMP_FILE && \
    echo "conda ${CONDA_VERSION}" >> $CONDA_DIR/conda-meta/pinned && \
    echo ". $CONDA_DIR/etc/profile.d/conda.sh" >> ~/.profile

SHELL ["/bin/bash", "-l", "-c"]

RUN conda init bash && \
    conda config --system --prepend channels conda-forge && \
    conda config --system --set auto_update_conda false && \
    conda config --system --set show_channel_urls true && \
    conda config --system --set channel_priority false && \
    conda update -n base -c defaults conda -qy && \
    conda update -n base -qy --all && \
    conda install -n base -qy pip notebook jupyter jupyterlab nb_conda_kernels tini && \
    conda list python | grep '^python ' | tr -s ' ' | cut -d '.' -f 1,2 | sed 's/$/.*/' >> $CONDA_DIR/conda-meta/pinned && \
    conda list tini | grep tini | tr -s ' ' | cut -d ' ' -f 1,2 >> $CONDA_DIR/conda-meta/pinned && \
    npm cache clean --force && \
    # Generate a notebook server config
    jupyter notebook --generate-config && \
    cd $HOME/tmp/ && \
    conda env create -f liveproject.yml && \
    conda env create -f liveproject-zipline.yml && \
    conda clean -afy && \
    rm -rf $CONDA_DIR/share/jupyter/lab/staging && \
    rm -rf $HOME/.cache/yarn && \
    find /opt/conda/ -follow -type f -name '*.a' -delete && \
    find /opt/conda/ -follow -type f -name '*.pyc' -delete && \
    find /opt/conda/ -follow -type f -name '*.js.map' -delete && \
    fix-permissions $CONDA_DIR && \
    fix-permissions $HOME && \
    rm -rf $HOME/tmp && \
    echo "alias lab='jupyter lab --ip 0.0.0.0 --no-browser'" >> ~/.bash_aliases && \
    echo "alias nb='jupyter notebook --ip 0.0.0.0 --no-browser'" >> ~/.bash_aliases && \
    cd $HOME/liveproject

ENV ZIPLINE_ROOT=$HOME/liveproject/.zipline

EXPOSE 8888

# Configure container startup
ENTRYPOINT ["tini", "-g", "--"]

# Copy local files as late as possible to avoid cache busting
COPY jupyter_notebook_config.py /etc/jupyter/

# Fix permissions on /etc/jupyter as root
USER root
RUN fix-permissions /etc/jupyter/

# Switch back to packt to avoid accidental container runs as root
USER $USER
WORKDIR $HOME/liveproject
