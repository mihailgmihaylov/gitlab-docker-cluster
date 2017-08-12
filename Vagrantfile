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
  ).freeze

  ROLES.each do |role|
    if role =~ /^database.*/
      config.vm.define(role) do |role_config|
        role_config.vm.provider :docker do |v, override|
          # v.build_dir = '.'
          # v.dockerfile = 'Dockerfile.postgres'
          v.image = "postgres:9.6"
          v.name = role
          v.privileged = true
        end
      end
    end
  end
end
