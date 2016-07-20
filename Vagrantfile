VAGRANTFILE_API_VERSION = "2"
box      = 'ubuntu/trusty64'
url      = 'https://atlas.hashicorp.com/ubuntu/boxes/trusty64'
JIRA_HOSTNAME = 'jira'
JIRA_IP       = '192.168.5.100'
JIRA_RAM      = '2048'

JENKINS_HOSTNAME = 'jenkins'
JENKINS_IP       = '192.168.5.101'
JENKINS_RAM      = '1024'

Vagrant::Config.run do |config|
  config.vm.box = box
  config.vm.box_url = url
  

end

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.define "jira" do |jiravm|
    jiravm.vm.host_name = JIRA_HOSTNAME
    jiravm.vm.network :private_network, ip: JIRA_IP 
    jiravm.vm.provider "virtualbox" do |v|
      v.customize [
        'modifyvm', :id,
        '--memory', JIRA_RAM
      ]
    end
  jiravm.vm.network "forwarded_port", guest: 8005, host: 8005
  jiravm.vm.network "forwarded_port", guest: 8080, host: 8081
  $script = <<SCRIPT
  wget -q https://www.atlassian.com/software/jira/downloads/binary/atlassian-jira-software-7.1.8-jira-7.1.8-x64.bin
  chmod +x atlassian-jira-software-7.1.8-jira-7.1.8-x64.bin
  wget -q https://raw.githubusercontent.com/wizzor/jira-jenkins/master/response.varfile
  sudo ./atlassian-jira-software-7.1.8-jira-7.1.8-x64.bin -q -varfile response.varfile
SCRIPT

      jiravm.vm.provision :shell, :inline => $script
  end

  config.vm.define "jenkins" do | jenkinsvm |
    
    ENV['LC_ALL']="en_US.UTF-8"
    jenkinsvm.vm.host_name = JENKINS_HOSTNAME
    jenkinsvm.vm.network :private_network, ip: JENKINS_IP
    jenkinsvm.vm.network "forwarded_port", guest: 8181, host: 9191
    jenkinsvm.vm.network "forwarded_port", guest: 8080, host: 9090
    jenkinsvm.vm.network "forwarded_port", guest: 7272, host: 8000

    jenkinsvm.vm.provider "virtualbox" do |v|
      v.customize [
        'modifyvm', :id,
        '--memory', JENKINS_RAM
      ]
    end
    jenkinsvm.vm.provision "ansible" do |ansible|
      ansible.verbose = "vvvv"
      ansible.playbook = "jenkins.yml"
      ansible.inventory_path = "ansible.host"
    end

  end

end