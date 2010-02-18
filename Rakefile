require "net/ftp"
require "timeout"
require "yaml"

def ftp_create_dir(ftp, target_dir)
  print "Creating dir #{target_dir}, "
  ftp.mkdir(target_dir)
  puts "done."
rescue Net::FTPPermError => e
  puts "directory already exists."
end

def ftp_copy_file(ftp, src_file, target_file)
  print "Copying #{src_file} -> #{target_file}, "
  ftp.putbinaryfile(src_file, target_file)
  puts "done."
end

def ftp_files(prefix_to_remove, source_file_list, target_dir, hostname, username, password)
  Net::FTP.open(hostname, username, password) do |ftp|
    ftp_create_dir(ftp, target_dir)
    source_file_list.each do |src_file|
      if prefix_to_remove
        target_file = src_file.pathmap("%{^#{prefix_to_remove},#{target_dir}}p")
      else
        target_file = src_file.pathmap("#{target_dir}%s%p")
      end
      if File.directory?(src_file)
        ftp_create_dir(ftp, target_file)
      else
        ftp_copy_file(ftp, src_file, target_file)
      end
    end
  end
end

namespace :deploy do
  credentials = YAML.load_file(".ftp.yaml")
  task :ftp do
    ftp_files("_site", FileList["_site/**/*"] - FileList["_site/assets/*"], "www", "ftp.start1m.ovh.net", credentials["user"], credentials["pass"])
  end
end
