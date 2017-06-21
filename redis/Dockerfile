FROM centos:7

ENV PUPPET_AGENT_VERSION="1.8.2" R10K_VERSION="2.2.2" 

RUN rpm -Uvh https://yum.puppetlabs.com/puppetlabs-release-pc1-el-7.noarch.rpm && \
    yum upgrade -y && \
    yum update -y && \
    yum install -y puppet-agent-"$PUPPET_AGENT_VERSION" && \
    mkdir -p /etc/puppetlabs/facter/facts.d/ && \
    yum clean all

RUN yum update -y && \
    yum install -y git && \
    /opt/puppetlabs/puppet/bin/gem install r10k:"$R10K_VERSION" --no-ri --no-rdoc && \
    yum clean all

COPY Puppetfile /Puppetfile
RUN /opt/puppetlabs/puppet/bin/r10k puppetfile install --moduledir /etc/puppetlabs/code/modules

COPY manifests /manifests

COPY hiera.yaml /hiera.yaml
COPY hieradata /hieradata

  
    
RUN yum update -y && \
    FACTER_hostname=redis /opt/puppetlabs/bin/puppet apply manifests/init.pp --verbose --show_diff --summarize --hiera_config=/hiera.yaml --app_management && \
    yum clean all

#RUN yum remove -y rsync git puppet\* openssh\*

#LABEL com.puppet.inventory="/inventory.json"
#RUN /opt/puppetlabs/bin/puppet module install puppetlabs-inventory && \
#    /opt/puppetlabs/bin/puppet inventory all > /inventory.json

EXPOSE 6000

CMD ["redis-server", "/etc/redis.conf", "--daemonize", "no"]
