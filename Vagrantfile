VAGRANT_API_VERSION = '2'.freeze

Vagrant.configure(VAGRANT_API_VERSION) do |config|
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = false
  config.hostmanager.manage_guest = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true
  ENV['VAGRANT_NO_PARALLEL'] = 'yes'

  NODES = %w(
    database-primary
    database-standby
    redis
    gitlab-1
    gitlab-2
    haproxy
  ).freeze

  NODES.each do |node|
    config.vm.define(node) do |node_config|
      # Creating GitLab Environment
      if node =~ /^gitlab.*/
        node_config.vm.provider :docker do |d, override|
          d.build_dir = 'gitlab-ce'
          d.name = node
          d.has_ssh = true
          d.privileged = true
        end
        node_config.vm.synced_folder "mountdirs/git-data", "/var/opt/gitlab/git-data"
        node_config.vm.synced_folder "mountdirs/ssh", "/var/opt/gitlab/.ssh"
        node_config.vm.synced_folder "mountdirs/uploads", "/var/opt/gitlab/gitlab-rails/uploads"
        node_config.vm.synced_folder "mountdirs/shared", "/var/opt/gitlab/gitlab-rails/shared"
        node_config.vm.synced_folder "mountdirs/builds", "/var/opt/gitlab/gitlab-ci/builds"
      elsif node =~ /^database.*/
        node_config.vm.provider :docker do |d, override|
          d.build_dir = 'postgres'
          d.name = node
          d.has_ssh = true
          d.privileged = true
        end
      elsif node == 'haproxy'
        node_config.vm.provider :docker do |d, override|
          d.build_dir = node
          d.name = node
          d.has_ssh = true
          d.privileged = true
        end
      node_config.vm.network :forwarded_port, guest: 80, host: 10080
      node_config.vm.network :forwarded_port, guest: 443, host: 10443
      node_config.vm.network :forwarded_port, guest: 8080, host: 18080
      node_config.vm.network :forwarded_port, guest: 22, host: 10022
      node_config.ssh.port = 2222
      else
        node_config.vm.provider :docker do |d, override|
          d.build_dir = node
          d.name = node
          d.has_ssh = true
          d.privileged = true
        end
      end

      # Provisioning Steps
      if node == 'database-primary'
        node_config.vm.provision :ansible, run: :always do |ansible|
          ansible.playbook = 'scripts/database-primary.yml'
        end
      end
    end
  end
end
