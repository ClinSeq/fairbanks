#Define the list of machines
machines = {
    :fairbanks => {
        :hostname => "fairbanks",
        :ipaddress => "10.10.10.93"
    },
}

#--------------------------------------
# General provisioning inline script
#--------------------------------------
$script = <<SCRIPT

# Start by making yum faster
sudo yum install yum-plugin-fastestmirror
sudo yum upgrade
sudo yum -y groupinstall "Development tools"

sudo yum install -y java-1.7.0-openjdk-devel
sudo yum install -y /vagrant/sbt/sbt-0.13.5.rpm
sudo yum install -y git nano mailx

# prereqs for for R and python: 
sudo yum install -y emacs git perl-ExtUtils-MakeMaker zlib zlib-devel libcurl-devel
sudo yum install -y readline-devel bzip2-devel sqlite-devel openssl-devel

sudo yum install -y emacs-nox samba gnuplot ImageMagick libxslt-devel libxml2-devel ncurses-devel libtiff-devel bzip2-devel zlib-devel perl-XML-LibXML perl-XML-LibXML-Common perl-XML-NamespaceSupport perl-XML-SAX perl-XML-Simple pigz

wget --no-clobber -P /tmp ftp://ftp.acc.umu.se/mirror/fedora/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
cd /tmp
sudo rpm -ivh epel-release-7-5.noarch.rpm

sudo yum install -y ant
sudo yum install -y htop


#Install the perl modules!
sudo yum install -y perl-PDL perl-PerlIO-gzip
sudo yum install -y perl-devel perl rsync dos2unix perl-CPAN gcc zlib-devel.x86_64 zlib.x86_64 expat-devel
curl -L http://cpanmin.us | perl - --sudo App::cpanminus
sudo /usr/local/bin/cpanm Archive::Zip
sudo /usr/local/bin/cpanm PerlIO::gzip
sudo /usr/local/bin/cpanm XML::Simple
sudo /usr/local/bin/cpanm MD5
sudo /usr/local/bin/cpanm ExtUtils::MakeMaker
sudo /usr/local/bin/cpanm --force Module::Compile
sudo /usr/local/bin/cpanm XML::LibXSLT
sudo /usr/local/bin/cpanm Archive::Extract
sudo /usr/local/bin/cpanm Time::HiRes
sudo /usr/local/bin/cpanm Compress::Zlib
sudo /usr/local/bin/cpanm Archive::Tar
sudo /usr/local/bin/cpanm Archive::Zip
sudo /usr/local/bin/cpanm CGI
sudo /usr/local/bin/cpanm DBI

sudo yum install -y ruby ruby-irb
sudo yum install -y readline-devel sqlite-devel
sudo yum install -y freetype-devel libpng-devel blas-devel lapack-devel

#gnu parallel
sudo yum install -y http://linuxsoft.cern.ch/cern/centos/7/cern/x86_64/Packages/parallel-20150522-1.el7.cern.noarch.rpm

###Install slurm

# create slurm user
sudo groupadd -g 500 slurm
sudo adduser -u 500 -g 500 --no-create-home slurm
sudo mkdir /usr/local/slurm
sudo chown slurm /usr/local/slurm
sudo chgrp slurm /usr/local/slurm
sudo mkdir /usr/local/slurm/state
sudo chown slurm /usr/local/slurm/state
sudo chgrp slurm /usr/local/slurm/state

# munge
sudo yum install -y munge munge-devel munge-libs pam-devel
dd if=/dev/urandom bs=1 count=1024 > /tmp/munge.key
sudo cp /tmp/munge.key /etc/munge/munge.key
sudo chown munge /etc/munge/munge.key
sudo chmod 600 /etc/munge/munge.key
rm /tmp/munge.key

# start the munge service
sudo service munge start
sudo chkconfig munge on

# slurm itself
cd /tmp
curl -O http://www.schedmd.com/download/archive/slurm-15.08.8.tar.bz2
sudo rpmbuild -ta slurm-15.08.8.tar.bz2
sudo yum localinstall -y /root/rpmbuild/RPMS/x86_64/slurm-15.08.8-1.el7.centos.x86_64.rpm /root/rpmbuild/RPMS/x86_64/slurm-plugins-15.08.8-1.el7.centos.x86_64.rpm /root/rpmbuild/RPMS/x86_64/slurm-munge-15.08.8-1.el7.centos.x86_64.rpm

#copy conf
sudo cp /vagrant/slurm/slurm.conf /etc/slurm/

#start the slurm service
sudo service slurm start
sudo chkconfig slurm on

# make accounting file world readable in order to enable `sacct -j jobid` for users to get job stats
sudo chmod a+r /usr/local/slurm/slurm_accounting.log

cd /tmp
wget https://download.microsoft.com/download/B/C/D/BCDD264C-7517-4B7D-8159-C99FC5535680/msodbcsql-13.0.0.0.tar.gz
tar xvfz msodbcsql-13.0.0.0.tar.gz
cd msodbcsql-13.0.0.0
./build_dm.sh --accept-warning
cd /tmp/unixODBC*/unixODBC-2.3.1
sudo make install
cd /tmp/msodbcsql-13.0.0.0
./install.sh verify
sudo ./install.sh install --accept-license

sudo mkdir -p /scratch/tmp/
sudo chmod a+w /scratch/tmp

sudo mkdir -p /nfs/ALASCCA/
sudo chmod a+w /nfs/ALASCCA

SCRIPT

#--------------------------------------
# Fire up the machine
#--------------------------------------
Vagrant.configure("2") do |global_config|
    machines.each_pair do |name, options|
        global_config.vm.define name do |config|
            #VM configurations
            config.vm.box = "bento/centos-7.1"
	    config.vm.hostname = "#{name}"
	    config.vm.network :private_network, ip: options[:ipaddress]

	    #VM specifications
	    config.vm.provider :virtualbox do |v|
		v.customize ["modifyvm", :id, "--memory", "6000", "--cpus", 2]
            end

            #VM provisioning
            config.vm.provision :shell,
                :inline => $script

        end
    end
end

