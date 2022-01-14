FROM cmict/workspace-full-vnc

# Install the latest rr.
RUN __RR_VERSION__="5.5.0" \
 && cd /tmp \
 && wget -qO rr.deb https://github.com/mozilla/rr/releases/download/${__RR_VERSION__}/rr-${__RR_VERSION__}-Linux-$(uname -m).deb \
 && sudo dpkg -i rr.deb \
 && rm -f rr.deb

# Install Firefox build dependencies
# One-line setup command from:
# https://firefox-source-docs.mozilla.org/setup/linux_build.html#one-line-bootstrapping
ENV SHELL=/bin/bash
RUN sudo apt-get update \
 && sudo apt-get install -y libpython3-dev uuid  \
 && sudo mkdir -p /workspace/.pip-modules && sudo chown gitpod:gitpod /workspace/.pip-modules \
 && ln -sf /home/gitpod/.cargo/bin/rustup /workspace/.cargo/bin/ \
 && rustup update stable \
 && ln -sf /home/gitpod/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/bin/* /home/gitpod/.cargo/bin/ \
 && ln -sf /home/gitpod/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/bin/* /workspace/.cargo/bin/ \
 && git clone --progress --single-branch --depth=1 https://github.com/mozilla/gecko-dev/ /tmp/gecko \
 && cd /tmp/gecko \
 && echo -e "\rY\rY\rY\rY\r"|./mach bootstrap --application-choice=browser \
 && sudo apt-get remove -y nodejs nodejs-doc \
 && sudo rm -rf /tmp/gecko /var/lib/apt/lists/*


# Install moz-phab
ENV SHELL=/bin/bash
RUN sudo apt-get update \
 && pip3 install --no-cache-dir MozPhab \
 && echo "PATH=\"\$PATH:$HOME/.local/bin/moz-phab\"" >> $HOME/.bashrc \
 && sudo rm -rf /var/lib/apt/lists/*

# Install git-cinnabar.
RUN git clone --depth 1 https://github.com/glandium/git-cinnabar $HOME/.git-cinnabar \
 && $HOME/.git-cinnabar/git-cinnabar download \
 && echo "\n# Add git-cinnabar to the PATH." >> $HOME/.bashrc \
 && echo "PATH=\"\$PATH:$HOME/.git-cinnabar\"" >> $HOME/.bashrc
ENV PATH $PATH:$HOME/.git-cinnabar

# Inject the path update to the top as bash completion needs to call rustc
RUN sed -i '1s|^|PATH=\"\$PATH:$HOME/.cargo/bin/\"\n|' $HOME/.bashrc

# Point to the doc on how to contribute
RUN echo "echo 'How to contribute:'"  >> $HOME/.bashrc \
 && echo "echo 'https://firefox-source-docs.mozilla.org/contributing/contribution_quickref.html'" >> $HOME/.bashrc
