# starting with the latest ubuntu image
FROM ubuntu:latest

# a few of environment variables
ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
ENV PATH /opt/conda/bin:$PATH
ENV ARCGIS_VERSION 1.6.0

# make sure everything is current to get started with and add some prerequesites
RUN apt-get update -n base -c defaults conda&& \
    apt-get upgrade -y -o Dpkg::Options::="--force-confold" && \
    apt-get install -y \
#        apt-utils \
        bzip2 \
        ca-certificates \
        libglib2.0-0 \
        libsm6 \
        libxext6 \
        libxrender1 \
        git \
        gnupg \
        mercurial \
        subversion \
        wget

# install and configure conda
RUN wget --quiet https://repo.anaconda.com/archive/Anaconda3-2019.03-Linux-x86_64.sh -O ~/anaconda.sh && \
    /bin/bash ~/anaconda.sh -b -p /opt/conda && \
    rm ~/anaconda.sh && \
    ln -s /opt/conda/etc/profile.d/conda.sh /etc/profile.d/conda.sh && \
    echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc && \
    echo "conda activate base" >> ~/.bashrc

# add channels to conda for packages to be installed
RUN conda config --prepend channels conda-forge && \
    conda config --prepend channels esri

# install nodejs since needed for arcgis map widget in jupyter lab
RUN curl -sL https://deb.nodesource.com/setup_11.x | bash - && \
    apt-get install -y nodejs

# install necessary packages
RUN conda install -y \
    arcgis==$ARCGIS_VERSION \
    shapely \
    geopandas

# post install commands for arcgis map widget to work in jupyter notebook and jupyerlab
RUN jupyter nbextension install --py --sys-prefix arcgis && \
    jupyter nbextension enable --py --sys-prefix arcgis && \
    jupyter labextension install @jupyter-widgets/jupyterlab-manager && \
    jupyter labextension install arcgis-map-ipywidget@$ARCGIS_VERSION

# use tini to make everythng play nice in containerland
RUN apt-get install -y curl grep sed dpkg && \
    TINI_VERSION=`curl https://github.com/krallin/tini/releases/latest | grep -o "/v.*\"" | sed 's:^..\(.*\).$:\1:'` && \
    curl -L "https://github.com/krallin/tini/releases/download/v${TINI_VERSION}/tini_${TINI_VERSION}.deb" > tini.deb && \
    dpkg -i tini.deb && \
    rm tini.deb

# ensure everything is cleaned up
RUN conda clean -a -y && \
    apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# run tini with any subsequent command line parameters
ENTRYPOINT [ "/usr/bin/tini", "--" ]

# if no commands are passed, start bash by default
CMD [ "/bin/bash" ]