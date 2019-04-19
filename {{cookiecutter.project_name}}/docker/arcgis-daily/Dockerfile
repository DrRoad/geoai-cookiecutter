# starting with the latest ubuntu image
FROM jupyter/minimal-notebook:latest

# add version as envronment variable
ENV ARCGIS_VERSION 1.6.0

# make sure everything is current to get started with and add nodejs for arcgis map widget in jupyter lab
USER root
RUN apt-get update && \
    apt-get upgrade -y -o Dpkg::Options::="--force-confold" && \
    apt-get -y install curl apt-utils && \
    curl -sL https://deb.nodesource.com/setup_11.x | bash - && \
    apt-get install -y nodejs && \
    apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
USER jovyan

# ensure conda is up to date, add channels, and install packages
RUN conda update -n base conda && \
    conda config --prepend channels conda-forge && \
    conda config --prepend channels http://zion/conda/master && \
    conda install -y \
        arcgis\
        beautifulsoup4 \
        bokeh \
        fiona \
        geopandas \
        html5lib \
        inflection \
        jupyterlab \
        keyring \
        libgdal \
        lxml \
        matplotlib \
        ncurses \
        numpy \
        pandas \
        pillow \
        pysal \
        pyshp \
        rtree \
        scikit-image \
        scikit-learn \
        seaborn \
        shapely \
        unzip

# post install commands for arcgis map widget to work in jupyter notebook and jupyer lab
RUN jupyter nbextension install --py --sys-prefix arcgis && \
    jupyter nbextension enable --py --sys-prefix arcgis && \
    jupyter labextension install @jupyter-widgets/jupyterlab-manager && \
    jupyter labextension install arcgis-map-ipywidget@$ARCGIS_VERSION

# copy the settings file to ensure jupyter lab runs in dark mode
RUN mkdir -p /home/jovyan/.jupyter/lab/user-settings/@jupyterlab/apputils-extension/
COPY themes.jupyterlab-settings /home/jovyan/.jupyter/lab/user-settings/@jupyterlab/apputils-extension/

# ensure everything is cleaned up
RUN conda clean -a -y

# if no commands are passed, start bash by default
CMD [ "/bin/bash" ]
