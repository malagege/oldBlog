---
title: 在 Ubutnu 安裝 Python 3.7 筆記
date: 2019-11-07 23:31:47
tags: [python,linux]
categories: Linux
---

最近在 Linux 安裝 Python 3.7 發現原生 `apt` 沒有包
需要手動編譯安裝
但遇到很多問題
在這邊小記

<!--more-->

各種 Linux 遇到安裝會不太一樣
網路的教學也不是都很能順利安裝
由其是 ctype 還是不知道什麼原因會有問題
但網路找的步驟用一用
還不是很確定哪一個項目解除
以下各種方法可以試試

## 安裝 Python3.7

首先，先檢查 openssl version 
不然跑 pip 時會出現

```
# Install requirements
sudo apt-get install -y build-essential
sudo apt-get install -y checkinstall
sudo apt-get install -y libreadline-gplv2-dev
sudo apt-get install -y libncursesw5-dev
sudo apt-get install -y libssl-dev
sudo apt-get install -y libsqlite3-dev
sudo apt-get install -y tk-dev
sudo apt-get install -y libgdbm-dev
sudo apt-get install -y libc6-dev
sudo apt-get install -y libbz2-dev
sudo apt-get install -y zlib1g-dev
sudo apt-get install -y openssl
sudo apt-get install -y libffi-dev
sudo apt-get install -y python3-dev
sudo apt-get install -y python3-setuptools
sudo apt-get install -y wget
sudo apt-get install zlib1g-dev # 
sudo apt-get install libffi-dev # ctype 問題 參考如下
# Prepare to build
mkdir /tmp/Python37
cd /tmp/Python37
# Pull down Python 3.7, build, and install
wget https://www.python.org/ftp/python/3.7.0/Python-3.7.0.tar.xz
tar xvf Python-3.7.0.tar.xz
cd /tmp/Python37/Python-3.7.0
./configure --enable-optimizations
sudo make altinstall

# openssl 安裝(看情行使用，越新的Linux 應該不會做到這個)
sudo apt-get install -y wget
mkdir /tmp/openssl
cd /tmp/openssl
wget https://www.openssl.org/source/openssl-1.0.2q.tar.gz
tar xvf openssl-1.0.2q.tar.gz
cd /tmp/openssl/openssl-1.0.2q
./config
make
sudo make install
sudo ln -sf /usr/local/ssl/bin/openssl `which openssl` 

## 這邊可以登出再登入 openssl version 有沒有換掉

sudo pip3.7 install --upgrade pip

```


### pip tls 問題

```
pip is configured with locations that require TLS/SSL, however the ssl module in Python is not available.
Collecting virtualenv
  Retrying (Retry(total=4, connect=None, read=None, redirect=None, status=None)) after connection broken by 'SSLError("Can't connect to HTTPS URL because the SSL module is not available.")': /simple/virtualenv/
  Retrying (Retry(total=3, connect=None, read=None, redirect=None, status=None)) after connection broken by 'SSLError("Can't connect to HTTPS URL because the SSL module is not available.")': /simple/virtualenv/
  Retrying (Retry(total=2, connect=None, read=None, redirect=None, status=None)) after connection broken by 'SSLError("Can't connect to HTTPS URL because the SSL module is not available.")': /simple/virtualenv/
  Retrying (Retry(total=1, connect=None, read=None, redirect=None, status=None)) after connection broken by 'SSLError("Can't connect to HTTPS URL because the SSL module is not available.")': /simple/virtualenv/
  Retrying (Retry(total=0, connect=None, read=None, redirect=None, status=None)) after connection broken by 'SSLError("Can't connect to HTTPS URL because the SSL module is not available.")': /simple/virtualenv/
  Could not fetch URL https://pypi.org/simple/virtualenv/: There was a problem confirming the ssl certificate: HTTPSConnectionPool(host='pypi.org', port=443): Max retries exceeded with url: /simple/virtualenv/ (Caused by SSLError("Can't connect to HTTPS URL because the SSL module is not available.")) - skipping
  Could not find a version that satisfies the requirement virtualenv (from versions: )
No matching distribution found for virtualenv
pip is configured with locations that require TLS/SSL, however the ssl module in Python is not available.
Could not fetch URL https://pypi.org/simple/pip/: There was a problem confirming the ssl certificate: HTTPSConnectionPool(host='pypi.org', port=443): Max retries exceeded with url: /simple/pip/ (Caused by SSLError("Can't connect to HTTPS URL because the SSL module is not available.")) - skipping
```
[python3.7安装后ssl问题-苏黎世1995的博客-51CTO博客](https://blog.51cto.com/13544424/2149473)

PS:後來發現是我的 ubuntu 版本太舊

選越大越不會遇到這個問題 ubuntu/xenial64 
我一開始用 hashicorp/precise64  畢竟名次很前面
[hashicorp - Vagrant Cloud](https://app.vagrantup.com/hashicorp)


### openssl 

上面已經有放，不過我放一下我網路找到的文章
但不太想整理，這邊可能有點亂

[Installing OpenSSL locally under your username – DreamHost](https://help.dreamhost.com/hc/en-us/articles/360001435926-Installing-OpenSSL-locally-under-your-username)


卡在 https://blog.csdn.net/github_38336924/article/details/82692075
./python: error while loading shared libraries: libssl.so.1.1: cannot open shared object file: Error 40
generate-posix-vars failed
make: *** [pybuilddir.txt] Error 1
＿
~~未解~~
請參考python3.7安裝步驟

[SSL issues with Python 3.7 Install From Source](https://joshspicer.com/python37-ssl-issue)
後來嘗試這篇

[How to install openssl 1.0.2 with default openssl (1.1.1) on Ubuntu 16.04? - Ask Ubuntu](https://askubuntu.com/questions/1000629/how-to-install-openssl-1-0-2-with-default-openssl-1-1-1-on-ubuntu-16-04)
\# (This will create a sym link to the new binaries)  
sudo ln -sf /usr/local/ssl/bin/openssl `which openssl` 

### ModuleNotFoundError: No module named ‘_ctypes’

未解之謎，但用一用就正常

```
ModuleNotFoundError: No module named ‘_ctypes’

sudo apt-get update
sudo apt-get upgrade
sudo apt-get dist-upgrade
sudo apt-get install build-essential python-dev python-setuptools python-pip python-smbus
sudo apt-get install build-essential libncursesw5-dev libgdbm-dev libc6-dev
sudo apt-get install zlib1g-dev libsqlite3-dev tk-dev
sudo apt-get install libssl-dev openssl
sudo apt-get install libffi-dev

[安装python3.7时候，报错ModuleNotFoundError: No module named '_ctypes' - wang725的专栏 - CSDN博客](https://blog.csdn.net/wang725/article/details/79905612)
or
[python - Python3: ImportError: No module named '_ctypes' when using Value from module multiprocessing - Stack Overflow](https://stackoverflow.com/questions/27022373/python3-importerror-no-module-named-ctypes-when-using-value-from-module-mul)

Installing libffi-dev and re-installing python3.7 fixed the problem for me.

to cleanly build py 3.7 libffi-dev is required or else later stuff will fail

這邊感覺編譯失敗要重新清除編譯`sudo make clean`
由於這一塊我不熟，或者刪掉 python 資料夾重新跑

sudo apt-get install libffi-dev
```

## 安裝/更新 pip

 sudo pip3.7 install --upgrade pip

[安装Python3.7过程中报错的解决方法 - 青衫博客 | QingShan Talk](https://qingshan.tk/2019/04/25/how-to-install-python3-on-old-os/)

https://help.dreamhost.com/hc/en-us/articles/115000702772-Installing-a-custom-version-of-Python-3
https://help.dreamhost.com/hc/en-us/articles/360001435926-Installing-OpenSSL-locally-under-your-username


## 小記我要安裝的環境
### 安裝 eyeD3

```
git clone https://github.com/nicfit/eyeD3.git
cd eyeD3
python setup.py install
sudo pip install pylast
```

or

```
sudo pip install eyeD3

sudo pip install pylast
```


eyeD3

### 安裝 ffmpeg

Ubuntu 14版可以執行安裝!!


> 在 Ubuntu 13 以後, FFmpeg 以經被 libav 取代.
> 在官方提供的軟體庫沒有辨法再簡單的 sudo apt-get install ffmpeg 了.
> 新的 libav-tools 套件也用 avconv 執行程式來取代原有的 ffmpeg. 
[在 Ubuntu 14.04 LTS 安裝 FFmpeg @ Welkin小窩 :: 痞客邦 ::](https://welkinchen.pixnet.net/blog/post/64803028-%E5%9C%A8-ubuntu-14.04-lts-%E5%AE%89%E8%A3%9D-ffmpeg)

sudo add-apt-repository ppa:jonathonf/ffmpeg-4
sudo apt-get update
sudo apt-get install ffmpeg


[How To Install FFmpeg on Ubuntu 18.04 & 16.04 LTS - TecAdmin](https://tecadmin.net/install-ffmpeg-on-linux/)
[ffmpeg/avconv 轉檔與影片連接範例 - 石頭閒語](http://rocksaying.tw/archives/22568176.html)



## docker 啟動 Python

 docker  run -it python:3.7 bash

就這麼簡單

Alpine 沒有安裝 bash
所以不能做上面指令

上面環境組裝 php + python 環境
我又遇到 pip3 的問題
但 php 源頭是用最新的 Linux
所以我暫時用 apt 安裝
退而求其次就降板

**這邊跳過不要看，有更好的寫法，不用降板**
留個紀錄
```
FROM php:7.3


# ensure local python is preferred over distribution python
ENV PATH /usr/local/bin:$PATH

# http://bugs.python.org/issue19846
# > At the moment, setting "LANG=C" on a Linux system *fundamentally breaks Python 3*, and that's not OK.
ENV LANG C.UTF-8

# extra dependencies (over what buildpack-deps already includes)
RUN apt-get update && apt-get install -y --no-install-recommends \
        tk-dev \
        uuid-dev \
        wget \
        gnupg \
    && rm -rf /var/lib/apt/lists/*

ENV GPG_KEY 0D96DF4D4110E5C43FBFB17F2D347EA6AA65421D
ENV PYTHON_VERSION 3.7.5

RUN set -ex \
    \
    && wget -O python.tar.xz "https://www.python.org/ftp/python/${PYTHON_VERSION%%[a-z]*}/Python-$PYTHON_VERSION.tar.xz" \
    && wget -O python.tar.xz.asc "https://www.python.org/ftp/python/${PYTHON_VERSION%%[a-z]*}/Python-$PYTHON_VERSION.tar.xz.asc" \
    && export GNUPGHOME="$(mktemp -d)" \
    && gpg --batch --keyserver ha.pool.sks-keyservers.net --recv-keys "$GPG_KEY" \
    && gpg --batch --verify python.tar.xz.asc python.tar.xz \
    && { command -v gpgconf > /dev/null && gpgconf --kill all || :; } \
    && rm -rf "$GNUPGHOME" python.tar.xz.asc \
    && mkdir -p /usr/src/python \
    && tar -xJC /usr/src/python --strip-components=1 -f python.tar.xz \
    && rm python.tar.xz \
    \
    && cd /usr/src/python \
    && gnuArch="$(dpkg-architecture --query DEB_BUILD_GNU_TYPE)" \
    && ./configure \
        --build="$gnuArch" \
        --enable-loadable-sqlite-extensions \
        --enable-optimizations \
        --enable-shared \
        --with-system-expat \
        --with-system-ffi \
        --without-ensurepip \
    && make -j "$(nproc)" \
# setting PROFILE_TASK makes "--enable-optimizations" reasonable: https://bugs.python.org/issue36044 / https://github.com/docker-library/python/issues/160#issuecomment-509426916
        PROFILE_TASK='-m test.regrtest --pgo \
            test_array \
            test_base64 \
            test_binascii \
            test_binhex \
            test_binop \
            test_bytes \
            test_c_locale_coercion \
            test_class \
            test_cmath \
            test_codecs \
            test_compile \
            test_complex \
            test_csv \
            test_decimal \
            test_dict \
            test_float \
            test_fstring \
            test_hashlib \
            test_io \
            test_iter \
            test_json \
            test_long \
            test_math \
            test_memoryview \
            test_pickle \
            test_re \
            test_set \
            test_slice \
            test_struct \
            test_threading \
            test_time \
            test_traceback \
            test_unicode \
        ' \
    && make install \
    && ldconfig \
    \
    && find /usr/local -depth \
        \( \
            \( -type d -a \( -name test -o -name tests \) \) \
            -o \
            \( -type f -a \( -name '*.pyc' -o -name '*.pyo' \) \) \
        \) -exec rm -rf '{}' + \
    && rm -rf /usr/src/python \
    \
    && python3 --version

# make some useful symlinks that are expected to exist
RUN cd /usr/local/bin \
    && ln -s idle3 idle \
    && ln -s pydoc3 pydoc \
    && ln -s python3 python \
    && ln -s python3-config python-config

RUN apt-get update && apt-get install -y --no-install-recommends \
        python3-pip \
        git \
        python3-setuptools

RUN pip3 install eyeD3

RUN pip3 install pylast==2.2.0

RUN apt-get install -y --no-install-recommends \
    ffmpeg

CMD ["bash"]
```

其實我有思考，是不是更正確是要把源頭 from image全改掉
全部在包，不過我還沒這樣測試


```

FROM php:7.3

RUN apt-get update -y && apt-get install -y openssl zip unzip git libmcrypt-dev
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN docker-php-ext-install opcache bcmath pdo_mysql mysqli


# ensure local python is preferred over distribution python
ENV PATH /usr/local/bin:$PATH

# http://bugs.python.org/issue19846
# > At the moment, setting "LANG=C" on a Linux system *fundamentally breaks Python 3*, and that's not OK.
ENV LANG C.UTF-8

# runtime dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
		ca-certificates \
		netbase \
	&& rm -rf /var/lib/apt/lists/*

ENV GPG_KEY 0D96DF4D4110E5C43FBFB17F2D347EA6AA65421D
ENV PYTHON_VERSION 3.7.5

RUN set -ex \
	\
	&& savedAptMark="$(apt-mark showmanual)" \
	&& apt-get update && apt-get install -y --no-install-recommends \
		dpkg-dev \
		gcc \
		libbz2-dev \
		libc6-dev \
		libexpat1-dev \
		libffi-dev \
		libgdbm-dev \
		liblzma-dev \
		libncursesw5-dev \
		libreadline-dev \
		libsqlite3-dev \
		libssl-dev \
		make \
		tk-dev \
		uuid-dev \
		wget \
		xz-utils \
		zlib1g-dev \
# as of Stretch, "gpg" is no longer included by default
		$(command -v gpg > /dev/null || echo 'gnupg dirmngr') \
	\
	&& wget -O python.tar.xz "https://www.python.org/ftp/python/${PYTHON_VERSION%%[a-z]*}/Python-$PYTHON_VERSION.tar.xz" \
	&& wget -O python.tar.xz.asc "https://www.python.org/ftp/python/${PYTHON_VERSION%%[a-z]*}/Python-$PYTHON_VERSION.tar.xz.asc" \
	&& export GNUPGHOME="$(mktemp -d)" \
	&& gpg --batch --keyserver ha.pool.sks-keyservers.net --recv-keys "$GPG_KEY" \
	&& gpg --batch --verify python.tar.xz.asc python.tar.xz \
	&& { command -v gpgconf > /dev/null && gpgconf --kill all || :; } \
	&& rm -rf "$GNUPGHOME" python.tar.xz.asc \
	&& mkdir -p /usr/src/python \
	&& tar -xJC /usr/src/python --strip-components=1 -f python.tar.xz \
	&& rm python.tar.xz \
	\
	&& cd /usr/src/python \
	&& gnuArch="$(dpkg-architecture --query DEB_BUILD_GNU_TYPE)" \
	&& ./configure \
		--build="$gnuArch" \
		--enable-loadable-sqlite-extensions \
		--enable-optimizations \
		--enable-shared \
		--with-system-expat \
		--with-system-ffi \
		--without-ensurepip \
	&& make -j "$(nproc)" \
# setting PROFILE_TASK makes "--enable-optimizations" reasonable: https://bugs.python.org/issue36044 / https://github.com/docker-library/python/issues/160#issuecomment-509426916
		PROFILE_TASK='-m test.regrtest --pgo \
			test_array \
			test_base64 \
			test_binascii \
			test_binhex \
			test_binop \
			test_bytes \
			test_c_locale_coercion \
			test_class \
			test_cmath \
			test_codecs \
			test_compile \
			test_complex \
			test_csv \
			test_decimal \
			test_dict \
			test_float \
			test_fstring \
			test_hashlib \
			test_io \
			test_iter \
			test_json \
			test_long \
			test_math \
			test_memoryview \
			test_pickle \
			test_re \
			test_set \
			test_slice \
			test_struct \
			test_threading \
			test_time \
			test_traceback \
			test_unicode \
		' \
	&& make install \
	&& ldconfig \
	\
	&& apt-mark auto '.*' > /dev/null \
	&& apt-mark manual $savedAptMark \
	&& find /usr/local -type f -executable -not \( -name '*tkinter*' \) -exec ldd '{}' ';' \
		| awk '/=>/ { print $(NF-1) }' \
		| sort -u \
		| xargs -r dpkg-query --search \
		| cut -d: -f1 \
		| sort -u \
		| xargs -r apt-mark manual \
	&& apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false \
	&& rm -rf /var/lib/apt/lists/* \
	\
	&& find /usr/local -depth \
		\( \
			\( -type d -a \( -name test -o -name tests \) \) \
			-o \
			\( -type f -a \( -name '*.pyc' -o -name '*.pyo' \) \) \
		\) -exec rm -rf '{}' + \
	&& rm -rf /usr/src/python \
	\
	&& python3 --version

# make some useful symlinks that are expected to exist
RUN cd /usr/local/bin \
	&& ln -s idle3 idle \
	&& ln -s pydoc3 pydoc \
	&& ln -s python3 python \
	&& ln -s python3-config python-config

# if this is called "PIP_VERSION", pip explodes with "ValueError: invalid truth value '<VERSION>'"
ENV PYTHON_PIP_VERSION 19.3.1
# https://github.com/pypa/get-pip
ENV PYTHON_GET_PIP_URL https://github.com/pypa/get-pip/raw/ffe826207a010164265d9cc807978e3604d18ca0/get-pip.py
ENV PYTHON_GET_PIP_SHA256 b86f36cc4345ae87bfd4f10ef6b2dbfa7a872fbff70608a1e43944d283fd0eee

RUN set -ex; \
	\
	savedAptMark="$(apt-mark showmanual)"; \
	apt-get update; \
	apt-get install -y --no-install-recommends wget; \
	\
	wget -O get-pip.py "$PYTHON_GET_PIP_URL"; \
	echo "$PYTHON_GET_PIP_SHA256 *get-pip.py" | sha256sum --check --strict -; \
	\
	apt-mark auto '.*' > /dev/null; \
	[ -z "$savedAptMark" ] || apt-mark manual $savedAptMark; \
	apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false; \
	rm -rf /var/lib/apt/lists/*; \
	\
	python get-pip.py \
		--disable-pip-version-check \
		--no-cache-dir \
		"pip==$PYTHON_PIP_VERSION" \
	; \
	pip --version; \
	\
	find /usr/local -depth \
		\( \
			\( -type d -a \( -name test -o -name tests \) \) \
			-o \
			\( -type f -a \( -name '*.pyc' -o -name '*.pyo' \) \) \
		\) -exec rm -rf '{}' +; \
	rm -f get-pip.py

RUN pip install eyeD3

    
CMD ["bash"]
```


### docker 修改經驗小記

1. RUN 這個指令 假如是跟上面有關的，記得要用以下方法

```
RUN git clone https://xxxx \
    && cd xxx
...
```
2. 安裝指令，記得要用`apt-get update && apt-get install -y --no-install-recommends \`

```
RUN apt-get update && apt-get install -y --no-install-recommends \
		ca-certificates \
		netbase \
```

3. 要合併 images ，建議找源頭一樣 image，這樣編譯 Dockerfile 比較無痛

4.  Slim,Stretch,Alpine 那些到底是什麼?

簡單說一下，Alpine 是體積更小的 Linux，所以她沒有 bash ，不能用 `docker run -it xxx bash` 真的不好用(我指的是 debug)
看到還是滿多人推薦用 Debian

**2020-01-09**
在 docker 內建沒有 bash

在 dockerfile 裝
```
RUN apk add --update bash && rm -rf /var/cache/apk/*
```
可以參考:Raspberry-Pi-使用-Docker-架設-flexget.md

更多資訊:
* [Benchmarking Debian vs Alpine as a Base Docker Image — Nick Janetakis](https://nickjanetakis.com/blog/benchmarking-debian-vs-alpine-as-a-base-docker-image)
* [嗯嗯對，用 slim 或是 alpine 確實可以省下很多空間，但同時他們也少裝了很多東西，可能會導致一些問題](https://medium.com/@larry850806/%E5%97%AF%E5%97%AF%E5%B0%8D-%E7%94%A8-slim-%E6%88%96%E6%98%AF-alpine-%E7%A2%BA%E5%AF%A6%E5%8F%AF%E4%BB%A5%E7%9C%81%E4%B8%8B%E5%BE%88%E5%A4%9A%E7%A9%BA%E9%96%93-%E4%BD%86%E5%90%8C%E6%99%82%E4%BB%96%E5%80%91%E4%B9%9F%E5%B0%91%E8%A3%9D%E4%BA%86%E5%BE%88%E5%A4%9A%E6%9D%B1%E8%A5%BF-%E5%8F%AF%E8%83%BD%E6%9C%83%E5%B0%8E%E8%87%B4%E4%B8%80%E4%BA%9B%E5%95%8F%E9%A1%8C-93c4cfa7b024)
* [如何挑选node docker镜像 - Ryan.Miao - 博客园](https://www.cnblogs.com/woshimrf/p/node-docker-image.html)
* [「译」如何选择python项目的基础docker镜像 - Wiki - DeanWu's Wiki Blog](https://pylixm.cc/posts/2019-06-26-base-image-python-docker-images.html)

5. Dockerfile 有多個可以獨立放在資料夾裡面，感覺在整合 docker-compose 會比較方便