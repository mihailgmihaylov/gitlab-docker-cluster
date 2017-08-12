FROM gitlab/gitlab-ce

# Adding sshd deamon configuration
RUN apt-get update && apt-get install -y sudo

RUN echo 'root:screencast' | chpasswd
RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

RUN useradd --create-home --shell /bin/bash vagrant \
  && echo vagrant:vagrant | chpasswd \
  && echo "PubkeyAuthentication yes" >> /assets/sshd_config \
  && sed -i 's/AllowUsers git/AllowUsers git vagrant/g' /assets/sshd_config

ADD https://raw.githubusercontent.com/mitchellh/vagrant/master/keys/vagrant.pub \
    /home/vagrant/.ssh/authorized_keys
RUN chown -R vagrant:vagrant /home/vagrant/.ssh \
  && chmod 0600 /home/vagrant/.ssh/authorized_keys \
  && chmod 0700 /home/vagrant/.ssh
ADD 01_vagrant /etc/sudoers.d/

# Copy the custom gitlab omnibus configuration file
COPY gitlab.rb /etc/gitlab/gitlab.rb

EXPOSE 443 80 22
CMD ["/assets/wrapper"]
