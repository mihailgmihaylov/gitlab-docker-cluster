VAGRANT_API_VERSION = '2'.freeze

Vagrant.configure(VAGRANT_API_VERSION) do |config|
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = false
  config.hostmanager.manage_guest = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true
  ENV['VAGRANT_NO_PARALLEL'] = 'yes'

  ROLES = %w(
    database-primary
    database-standby
    redis
    gitlab-1
    gitlab-2
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
      elsif role =~ /gitlab/
        role_config.vm.provider :docker do |d, override|
          # d.image = "gitlab/gitlab-ce"
          d.build_dir = 'gitlab-ce'
          # d.volumes = ["/var/docker/gitlab:/data"]
          d.name = role
          d.has_ssh = true
          d.privileged = true
        end
        role_config.vm.synced_folder "mountdirs/git-data", "/var/opt/gitlab/git-data"
        role_config.vm.synced_folder "mountdirs/ssh", "/var/opt/gitlab/.ssh"
        role_config.vm.synced_folder "mountdirs/uploads", "/var/opt/gitlab/gitlab-rails/uploads"
        role_config.vm.synced_folder "mountdirs/shared", "/var/opt/gitlab/gitlab-rails/shared"
        role_config.vm.synced_folder "mountdirs/builds", "/var/opt/gitlab/gitlab-ci/builds"
        # role_config.vm.network :forwarded_port, guest: 80, host: 10080
        # role_config.vm.network :forwarded_port, guest: 443, host: 10443
      end

      if role == 'database-primary'
        role_config.vm.provision :ansible, run: :always do |ansible|
          ansible.playbook = 'scripts/database-primary.yml'
        end
      end
    end
  end
end
