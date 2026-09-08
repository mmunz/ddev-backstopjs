#ddev-generated
ARG BASE_IMAGE
FROM $BASE_IMAGE

# Add custom entrypoint
COPY ./entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

# delete the default 'node' user with uid 1000 and add current ddev user
ARG username
ARG uid
ARG gid
RUN userdel -r node
RUN (groupadd --gid $gid "$username" || groupadd "$username" || true) && (useradd  -l -m -s "/bin/bash" --gid "$username" --comment '' --uid $uid "$username" || useradd  -l -m -s "/bin/bash" --gid "$username" --comment '' "$username" || useradd  -l -m -s "/bin/bash" --gid "$gid" --comment '' "$username" || useradd -l -m -s "/bin/bash" --comment '' $username )

# Fix expired Bullseye repositories across all APT source configurations
RUN sed -i 's/deb.debian.org/archive.debian.org/g' /etc/apt/sources.list /etc/apt/sources.list.d/* 2>/dev/null || true && \
    sed -i 's/security.debian.org/archive.debian.org/g' /etc/apt/sources.list /etc/apt/sources.list.d/* 2>/dev/null || true && \
    sed -i '/bullseye-updates/d' /etc/apt/sources.list /etc/apt/sources.list.d/* 2>/dev/null || true

# Ensure the sudoers directory exists before the echo command runs
RUN mkdir -p /etc/sudoers.d

# Add sudo and sudoers in manner similar to other ddev containers
RUN apt update && apt install -y sudo; apt clean -qq && rm -rf /var/lib/apt/lists/*; echo "ALL ALL=NOPASSWD: ALL" > /etc/sudoers.d/ddev-backstop && chmod 440 /etc/sudoers.d/ddev-backstop

RUN npm install -g minimist

# Install playwright browsers
RUN npx playwright install-deps && npm cache clean --force; apt clean -qq && rm -rf /var/lib/apt/lists/*
