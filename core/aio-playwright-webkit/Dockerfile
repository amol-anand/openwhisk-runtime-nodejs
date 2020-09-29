#
# Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.
# The ASF licenses this file to You under the Apache License, Version 2.0
# (the "License"); you may not use this file except in compliance with
# the License.  You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#

FROM ubuntu:bionic

# 1. Install node12
RUN apt-get update && apt-get install -y curl && \
    curl -sL https://deb.nodesource.com/setup_12.x | bash - && \
    apt-get install -y nodejs

# 2. Install WebKit dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    libwoff1 \
    libopus0 \
    libwebp6 \
    libwebpdemux2 \
    libenchant1c2a \
    libgudev-1.0-0 \
    libsecret-1-0 \
    libhyphen0 \
    libgdk-pixbuf2.0-0 \
    libegl1 \
    libnotify4 \
    libxslt1.1 \
    libevent-2.1-6 \
    libgles2 \
    libvpx5 \
    libxcomposite1 \
    libatk1.0-0 \
    libatk-bridge2.0-0 \
    libepoxy0 \
    libgtk-3-0 \
    libharfbuzz-icu0

# 3. Install gstreamer and plugins to support video playback in WebKit.
RUN apt-get update && apt-get install -y --no-install-recommends \
    libgstreamer-gl1.0-0 \
    libgstreamer-plugins-bad1.0-0 \
    gstreamer1.0-plugins-good

# # 4. Install Chromium dependencies
# RUN apt-get update && apt-get install -y --no-install-recommends \
#     libnss3 \
#     libxss1 \
#     libasound2 \
#     fonts-noto-color-emoji \
#     libxtst6

# # 5. Install Firefox dependencies
# RUN apt-get update && apt-get install -y --no-install-recommends \
#     libdbus-glib-1-2 \
#     libxt6

# # 6. Install ffmpeg to bring in audio and video codecs necessary for playing videos in Firefox.
# RUN apt-get update && apt-get install -y --no-install-recommends \
#     ffmpeg

# 7. Add user so we don't need --no-sandbox in Chromium
# RUN groupadd -r runtimeuser && useradd -r -g runtimeuser -G audio,video runtimeuser \
#     && mkdir -p /home/runtimeuser/Downloads \
#     && chown -R runtimeuser:runtimeuser /home/runtimeuser \
#     && chmod -R 777 /home/runtimeuser

# 8. (Optional) Install XVFB if there's a need to run browsers in headful mode
RUN apt-get update && apt-get install -y --no-install-recommends \
    xvfb

# 9. Run everything after as non-privileged user.
# USER runtimeuser

# === BAKE BROWSERS INTO IMAGE ===

# 1. Add tip-of-tree Playwright package to install its browsers.
#    The package should be built beforehand from tip-of-tree Playwright.
#COPY --chown=runtimeuser ./playwright.tar.gz /home/pwuser/playwright.tar.gz

# 2. Install playwright and then delete the installation.
#    Browsers will remain downloaded in `/home/runtimeuser/.cache/ms-playwright`.
# RUN mkdir /home/runtimeuser/tmp && cd /home/runtimeuser/tmp && npm init -y && \
#     npm i ../playwright.tar.gz && \
#     cd ../ && rm -rf tmp && rm /home/runtimeuser/playwright.tar.gz

# 2. Install playwright
# 
RUN mkdir /pw-tmp && cd /pw-tmp && npm init -y && \
npm i -D playwright-webkit

# Initial update and some basics.
#
RUN apt-get update && apt-get install -y \
    unzip \
    && rm -rf /var/lib/apt/lists/*
    
# Install the stuff needed for runtime to work

# Add sources and copy the package.json to root container,
# so npm packages from user functions take precendence.
#
WORKDIR /nodejsAction
ADD  . /nodejsAction/
COPY package.json /

# Customize runtime with additional packages.
# Install package globally so user packages can override.
#
RUN cd / && npm install --no-package-lock --production \
    && npm cache clean --force

EXPOSE 8080

CMD node --expose-gc app.js