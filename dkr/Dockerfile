# cd ~/dkr && docker build -t steem .
# docker run -p 0.0.0.0:2001:2001 -v $PWD/data:/steem -d -t steem

FROM ubuntu:xenial
RUN apt-get update && \
	apt-get install -y gcc-4.9 g++-4.9 cmake make libbz2-dev libdb++-dev libdb-dev && \
	apt-get install -y libssl-dev openssl libreadline-dev autoconf libtool git && \
	apt-get install -y autotools-dev build-essential g++ libbz2-dev libicu-dev python-dev wget doxygen python3 python3-dev python3-pip curl && \
        apt-get clean -qy && \
        pip3 install jinja2


ARG muse_version=c5f832e2ecd81e23c427f9d7f1db146e86aac1fe

# MUSE is strange, need to use custom boost

RUN apt-get remove -y libboost-all-dev && apt-get autoremove -y && \
 mkdir -p ~/dev/third-party && \
 cd ~/dev/third-party && \
 rm -rf boost_1_60_0* boost-1.60.0* && \
 wget https://sourceforge.net/projects/boost/files/boost/1.60.0/boost_1_60_0.tar.gz && \
 tar xvf boost_1_60_0.tar.gz && \
 mkdir boost-1.60.0_prefix && \
 cd boost_1_60_0 && \
 ./bootstrap.sh --prefix=$BOOST_ROOT && \
 ./b2 install && \
 cd .. && \
 rm -rf boost_1_60_0 boost_1_60_0.tar.gz

RUN cd ~ && \
	git clone https://github.com/themuseblockchain/Muse-Source.git && \
	cd Muse-Source && \
	git checkout ${muse_version} && \
	git submodule update --init --recursive && \
        cd ~/Muse-Source && \
	cmake	-G "Unix Makefiles" -DCMAKE_BUILD_TYPE=Debug . \
		-DBoost_DETAILED_FAILURE_MSG=TRUE \
		-DBOOST_ROOT=$(realpath ../boost-1.60.0_prefix) && \
	cmake --build . --target all -- -j 3 && cp -R programs/* /usr/local/bin/ && rm -rf ~/Muse-Source
VOLUME /muse
WORKDIR /muse

RUN echo "Please configure me! You need to mount a data directory onto /muse of this container to it to function correctly. (if you're using Muse-in-a-box most of this is handled automatically)"
CMD ["sh", "-c", "/usr/local/bin/mused/mused"]
