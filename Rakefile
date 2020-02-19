IMAGE="xenial_sshd"
PORTS=["32770" , "32769", "32768"]

task :default => [:run]

desc "create docker images #{IMAGE}"
task :create do
    sh "docker run -d --name ssh1 -P #{IMAGE}"
    sh "docker run -d --name ssh2 -P #{IMAGE}"
    sh "docker run -d --name ssh3 -P #{IMAGE}"
end

desc "run docker image #{IMAGE} with a shell"
task :shell do
    puts "needs to be fixed"
    # sh "docker run -it #{IMAGE} /bin/bash"
end

desc "build docker image #{IMAGE}"
task :build do
    sh "docker build -t #{IMAGE} ."
end

desc "copy ssh key to image #{IMAGE}"
task :key do
    PORTS.each { |port|
    sh "scp -P #{port} ~/.ssh/id_rsa.pub root@localhost:/root/.ssh/authorized_keys"
    }
end