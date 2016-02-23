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
sudo yum install -y git nano

# prereqs for for R and python: 
sudo yum install -y emacs git perl-ExtUtils-MakeMaker zlib zlib-devel libcurl-devel
sudo yum install -y readline-devel bzip2-devel sqlite-devel openssl-devel

sudo yum install -y emacs-nox samba gnuplot PyXML ImageMagick libxslt-devel libxml2-devel ncurses-devel libtiff-devel bzip2-devel zlib-devel perl-XML-LibXML perl-XML-LibXML-Common perl-XML-NamespaceSupport perl-XML-SAX perl-XML-Simple pigz

wget --no-clobber -P /tmp  http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
cd /tmp
sudo rpm -ivh epel-release-6-8.noarch.rpm

sudo yum install -y ant ant-nodeps

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
sudo /usr/local/bin/cpanm PDL
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

# First all the munge stuff
sudo yum install -y openssl-devel
wget --no-clobber -P /tmp https://munge.googlecode.com/files/munge-0.5.11.tar.bz2

cd /tmp
rpmbuild -tb --clean munge-0.5.11.tar.bz2
sudo rpm -ivh /root/rpmbuild/RPMS/x86_64/munge-*
dd if=/dev/urandom bs=1 count=1024 > /tmp/munge.key
sudo cp /tmp/munge.key /etc/munge/munge.key
sudo service munge start
sudo chkconfig munge on

# Download the slurm source
wget --no-clobber -P /tmp https://github.com/SchedMD/slurm/archive/slurm-14-03-7-1.tar.gz
cd /tmp
tar -z -x -f slurm-*
cd slurm-*/
./configure --enable-multiple-slurmd --enable-front-end
make
sudo make install
sudo cp /vagrant/slurm/slurm.conf /usr/local/etc/
sudo cp /vagrant/slurm/slurm.service.txt /etc/rc.d/init.d/slurm
sudo chmod a+x /etc/rc.d/init.d/slurm
sudo chkconfig --add slurm
sudo service slurm start
sudo chkconfig slurm on

# let vagrant user own it's bin
sudo chown -R vagrant:vagrant /home/vagrant/bin

SCRIPT

#--------------------------------------
# Fire up the machines
#--------------------------------------
Vagrant.configure("2") do |global_config|
    machines.each_pair do |name, options|
        global_config.vm.define name do |config|
            #VM configurations
            config.vm.box = "bento/centos-6.7"
            config.vm.hostname = "#{name}"
            config.vm.network :private_network, ip: options[:ipaddress]

            config.vm.synced_folder "/proj/b2010040/python/pyautoseq", "~/pyautoseq"
            config.vm.synced_folder "~/bin/autoseq", "/home/vagrant/bin/autoseq"
            config.vm.synced_folder "~/bin/pipeline-tools", "/home/vagrant/bin/pipeline-tools"
            config.vm.synced_folder "~/projects", "/home/vagrant/projects"
            config.vm.synced_folder "/proj", "/proj"

            #VM specifications
            config.vm.provider :virtualbox do |v|
                v.customize ["modifyvm", :id, "--memory", "8192", "--cpus", 2]
            end

            #VM provisioning
            config.vm.provision :shell,
                :inline => $script

        end
    end
end

