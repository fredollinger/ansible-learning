INSTANCE="xenial_sshd"
IMAGES=["ssh1", "ssh2", "ssh3"]
CLEAN_FILES=["ansible-hosts", "etc-hosts"]

task :default => [:run]

desc "create docker images"
task :create do
    IMAGES.each { |image|
        sh "docker run -d --name image -P #{INSTANCE}"
    }
end

desc "run docker images with a shell"
task :shell do
    puts "needs to be fixed"
    # sh "docker run -it #{IMAGE} /bin/bash"
end

desc "build docker image #{INSTANCE}"
task :build do
    sh "docker build -t #{INSTANCE} ."
end

desc "copy ssh key to images"
task :keys do
    IMAGES.each { |image|
        puts "scp ~/.ssh/id_rsa.pub root@#{image}:/root/.ssh/authorized_keys"
    }
end

desc "create both /etc/hosts and /etc/ansible/hosts files"
task :hosts do
    sh "cp /etc/ansible/hosts ansible-hosts"
    sh "cp /etc/hosts etc-hosts"
    IMAGES.each { |image|
        sh "echo `docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' #{image}` #{image} >> etc-hosts"
        sh "echo #{image} >> ansible-hosts"
    }
end

desc "clean temp files"
task :clean do
  CLEAN_FILES.each { |file|
      rm_rf file
  }
end
