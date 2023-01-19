FROM ghcr.io/gofractally/psibase-ubuntu-2004-builder:latest

# Use bash, prettify terminal, git completion
ENV SHELL /bin/bash
SHELL ["/bin/bash", "-c"]
RUN echo $'\n\
parse_git_branch() {\n\
  git branch 2> /dev/null | sed -e "/^[^*]/d" -e "s/* \\(.*\\)/ (\\1)/"\n\
} \n\
export PS1="\u@\h \W\[\\033[32m\\]\\$(parse_git_branch)\\[\\033[00m\\] $ "\n\
if [ -f ~/.git-completion.bash ]; then\n\
  . ~/.git-completion.bash\n\
fi\n\
 \n\
alias ll="ls -alF"\n\
alias ls="ls --color=auto"\n\
' >> /root/.bashrc

RUN mkdir -p \
    /root/deps \
    /root/projects

# Install deps
# Not sure what this is for, or if it is needed:
#WORKDIR /root
#RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
#RUN echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list
RUN export DEBIAN_FRONTEND=noninteractive   \
    && apt-get update                       \
    && apt-get install -yq                  \
      # gnupg (might be covered by the base img) \
        wget                                \
        xz-utils                            \
    && apt-get clean -yq                    \
    && rm -rf /var/lib/apt/lists/*

# Psidk
WORKDIR /root/deps
RUN wget https://github.com/gofractally/psibase/releases/download/rolling-release/psidk-linux.tar.gz \
    && tar xf psidk-linux.tar.gz         \
    && rm psidk-linux.tar.gz
ENV PSIDK_PREFIX=/root/deps/psidk
ENV WASI_SDK_PREFIX=/opt/wasi-sdk-14.0
ENV PATH=$PSIDK_PREFIX/bin:$WASI_SDK_PREFIX/bin:/root/app-generator/scripts:$PATH

# Add app generator
ADD app-generator /root/app-generator
RUN chmod -R 0700 /root/app-generator

# Set Psinode URL
ENV PSINODE_URL=http://jamesmart_psidekick-desktop-extension-service:8080/
