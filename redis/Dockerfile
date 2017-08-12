FROM redis:4.0

RUN apt-get update && apt-get install -y openssh-server \
  sudo

RUN mkdir /var/run/sshd
RUN echo 'root:screencast' | chpasswd
RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config

RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

RUN useradd --create-home --shell /bin/bash vagrant \
  && echo vagrant:vagrant | chpasswd \
  && echo "PubkeyAuthentication yes" >> /etc/ssh/sshd_config

ADD https://raw.githubusercontent.com/mitchellh/vagrant/master/keys/vagrant.pub \
    /home/vagrant/.ssh/authorized_keys
RUN chown -R vagrant:vagrant /home/vagrant/.ssh \
  && chmod 0600 /home/vagrant/.ssh/authorized_keys \
  && chmod 0700 /home/vagrant/.ssh
ADD 01_vagrant /etc/sudoers.d/

EXPOSE 5432
EXPOSE 22
CMD docker-entrypoint.sh redis-server & /usr/sbin/sshd -D
