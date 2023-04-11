#
# NOTE: THIS DOCKERFILE IS GENERATED VIA "apply-templates.sh"
#
# PLEASE DO NOT EDIT IT DIRECTLY.
#

FROM buildpack-deps:bullseye


#---------------------------------- Compile python binary ------------------------------------
WORKDIR /app

RUN set -eux; \
	apt-get update; \
	apt-get install git; \
	git clone https://github.com/python/cpython.git; \
	cd cpython; \
	git checkout v3.11.2; \
	./configure --with-lto --enable-optimizations --with-openssl=/usr; \
	make \
	; \
	ls -l /app/cpython/python

# Set python binary path
ENV PYTHON_BINARY /app/cpython/python


#-------------------------------------- Install pip3 -----------------------------------------
# Install pip3
# if this is called "PIP_VERSION", pip explodes with "ValueError: invalid truth value '<VERSION>'"
ENV PYTHON_PIP_VERSION 22.3.1
# https://github.com/docker-library/python/issues/365
ENV PYTHON_SETUPTOOLS_VERSION 65.5.1
# https://github.com/pypa/get-pip
ENV PYTHON_GET_PIP_URL https://github.com/pypa/get-pip/raw/d5cb0afaf23b8520f1bbcfed521017b4a95f5c01/public/get-pip.py
ENV PYTHON_GET_PIP_SHA256 394be00f13fa1b9aaa47e911bdb59a09c3b2986472130f30aa0bfaf7f3980637

RUN set -eux; \
	\
	wget -O get-pip.py "$PYTHON_GET_PIP_URL"; \
	echo "$PYTHON_GET_PIP_SHA256 *get-pip.py" | sha256sum -c -; \
	\
	export PYTHONDONTWRITEBYTECODE=1; \
	\
	python3 get-pip.py \
		--disable-pip-version-check \
		--no-cache-dir \
		--no-compile \
		"pip==$PYTHON_PIP_VERSION" \
		"setuptools==$PYTHON_SETUPTOOLS_VERSION" \
	; \
	rm -f get-pip.py; \
	\
	pip --version

# Install pyperformance
RUN python3 -m pip install pyperformance


#------------------------------- pre-download all the pip debs -------------------------------
RUN pyperformance run -p "$PYTHON_BINARY" --inherit-environ http_proxy,https_proxy --affinity 4 -r


ENTRYPOINT ["pyperformance","run","-p","/app/cpython/python","--inherit-environ", "http_proxy,https_proxy", "-r"]

