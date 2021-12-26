FROM centos:7


RUN yum -y install mysql

RUN yum -y install python3 \
      python3-pip  \
      python3-setuptools \
    && pip3 install --upgrade awscli


RUN yum -y install openssh-server

RUN useradd remote_user && \
    echo "remote_user:1234" | passwd remote_user --stdin && \
    mkdir /home/remote_user/.ssh && \
    chmod 700 /home/remote_user/.ssh

COPY remote-key.pub /home/remote_user/.ssh/authorized_keys

RUN chown remote_user:remote_user -R /home/remote_user/.ssh && \
    chmod 600 /home/remote_user/.ssh/authorized_keys

RUN /usr/sbin/sshd-keygen
CMD /usr/sbin/sshd -D