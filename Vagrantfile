Vagrant.configure("2") do |config|

  arch = ENV['ARCH'] || ''
  if arch == ''
    config.vm.box = 'bento/ubuntu-22.04'
  else
    config.vm.box = 'bento/ubuntu-20.04-arm64'
  end
  
  config.vm.provision "ansible_local" do |ansible|
    ansible.playbook = "playbook.yml"
  end

  hostname = ENV['SET_HOSTNAME']
  if hostname == ''
    hostname = 'rails.dev'
  end

  config.vm.hostname = hostname

  config.vm.box_check_update = false

  config.vm.network "private_network", type: "dhcp"

  config.vm.provider "parallels" do |v|

    v.name = hostname
     
    host = RbConfig::CONFIG['host_os']

    # Give VM 1/4 system memory & access to all cpu cores on the host
    if host =~ /darwin/
      cpus = `sysctl -n hw.ncpu`.to_i
      # sysctl returns Bytes and we need to convert to MB
      mem = `sysctl -n hw.memsize`.to_i / 1024 / 1024 / 2
    elsif host =~ /linux/
      cpus = `nproc`.to_i
      # meminfo shows KB and we need to convert to MB
      mem = `grep 'MemTotal' /proc/meminfo | sed -e 's/MemTotal://' -e 's/ kB//'`.to_i / 1024 / 4
    else # sorry Windows folks, I can't help you
      cpus = 2
      mem = 1024
    end

    v.memory = mem
    v.cpus = cpus

    config.hostmanager.enabled = true
    config.hostmanager.manage_host = true
    config.hostmanager.manage_guest = true
    config.hostmanager.ignore_private_ip = false
    config.hostmanager.include_offline = true

    config.hostmanager.ip_resolver = proc do |machine|
      result = ""
      machine.communicate.execute("ifconfig eth1") do |type, data|
        result << data if type == :stdout
      end
      (ip = /inet (\d+\.\d+\.\d+\.\d+)/.match(result)) && ip[1]
    end

  end

end
