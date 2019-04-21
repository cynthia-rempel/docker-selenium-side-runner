FROM registry.hub.docker.com/library/centos:centos7
# TODO (if all else fails) https://medium.com/dot-debug/running-chrome-in-a-docker-container-a55e7f4da4a8
LABEL description="vcsim - A vCenter and ESXi API based simulator"

ENV container=docker \
    node_env=production \
    TZ=UTC \
    PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/opt/rh/rh-nodejs10/root/bin \
    LD_LIBRARY_PATH=/opt/rh/rh-nodejs10/root/usr/lib64:/opt/rh/rh-nodejs10/root/usr/lib \
    PYTHONPATH=/opt/rh/rh-nodejs10/root/usr/lib/python2.7/site-packages \
    NODE_PATH=/opt/rh/rh-nodejs10/root/usr/lib/node_modules

# Install EPEL software collections
RUN rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7 \
    && yum -y update \
    && yum -y install centos-release-scl epel-release wget -y \
    && rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7 \
    && rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-SIG-SCLo

# Install selenium-side-runner
RUN yum -y install \
         chromedriver \
         rh-nodejs10 \
         unzip \
    && wget \
    https://dl.google.com/linux/direct/google-chrome-stable_current_x86_64.rpm \
    && yum -y localinstall google-chrome-stable_current_x86_64.rpm \
    && rm -rf google-chrome-stable_current_x86_64.rpm
RUN npm install yarn --global | tee yarn-install.log \
    && yarn global add selenium-side-runner | tee selenium-side-runner-install.log

# Smoke test selenium-side-runner, test using chrome, which requires non-root user
RUN useradd chrome-user
COPY files /
RUN chown -R chrome-user:chrome-user /home/chrome-user
USER chrome-user
WORKDIR /home/chrome-user
RUN selenium-side-runner -c "browserName='chrome' chromeOptions.args=[headless,no-sandbox,disable-gpu,disable-dev-shm-usage,remote-debugging-port=4444]" jsonplaceholder.side \
    && echo "hello2"
CMD ["init"]

