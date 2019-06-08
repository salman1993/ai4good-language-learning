FROM python:3.7 as base

ENV HOME /root

ENV LC_ALL C.UTF-8
ENV LANG C.UTF-8

ENV VIRTUAL_ENV "/srv/venv"
ENV PATH "${VIRTUAL_ENV}/bin:${PATH}"
ENV POETRY_VERSION "0.12.11"

RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y -q \
        wget \
        build-essential \
        libc6-dev \
        git \
        curl \
        vim \
    && rm -rf /var/lib/apt/lists/*


RUN ln -s /usr/bin/python3.7 /usr/bin/python
RUN python -m pip install -U pip
RUN ln -sf /usr/bin/pip3.7 /usr/bin/pip


WORKDIR /usr/local
COPY pyproject.toml poetry.lock /usr/local/

RUN pip install poetry==${POETRY_VERSION}
#RUN python -m venv "${VIRTUAL_ENV}"

RUN poetry install --no-dev

RUN python -m spacy download en


##########
# Dev
##########

FROM base as dev

# Install in a separate layer so source changes doesn't rerun it.
WORKDIR /usr/local
RUN poetry install

WORKDIR /srv/app
COPY . .


ENV PYTHONPATH "/srv/app:${PYTHONPATH}"
# Install again go the package will be installed into the python path
RUN poetry install

FROM dev as jupyter-lab

ENTRYPOINT ["jupyter", "lab", "--no-browser", "--allow-root", "--ip=0.0.0.0"]