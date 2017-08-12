VAGRANT_API_VERSION = '2'.freeze

Vagrant.configure(VAGRANT_API_VERSION) do |config|
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = false
  config.hostmanager.manage_guest = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true

  ROLES = %w(
    database-primary
    database-standby
    redis
  ).freeze

  ROLES.each do |role|
    config.vm.define(role) do |role_config|
      if role =~ /^database.*/
        role_config.vm.provider :docker do |d, override|
          d.build_dir = 'postgres'
          # d.image = "postgres:9.6"
          # d.volumes = ["/var/docker/redis:/data"]
          d.name = role
          d.has_ssh = true
          d.privileged = true
        end
      elsif role == 'redis'
        role_config.vm.provider :docker do |d, override|
          d.build_dir = 'redis'
          # d.volumes = ["/var/docker/redis:/data"]
          d.name = role
          d.has_ssh = true
          d.privileged = true
        end
      end


      if role == 'database-primary'
        role_config.vm.provision :ansible, run: :always do |ansible|
          ansible.playbook = 'scripts/database-primary.yml'
        end
      end
    end
  end
end
