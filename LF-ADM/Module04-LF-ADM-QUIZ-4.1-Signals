# Quiz 4.1: SIGKILL Signal
1. Write a list of files opened for read or write from xrdp program (not xrdp-sesman) to the file /opt/quiz41/answers/file_audit.txt , one per line. The output file has been created for your convenience. Be sure to include the absolute path to each file

2. The xrdp daemon process is listening on a TCP port. Write the port number (and only the port number) that the xrdp daemon is listening on the file /opt/quiz41/answers/port.txt .

3. After reading the documentation for the software you note that in order to have the xrdp daemon perform its garbage collection routine, it must be sent a SIGKILL signal. Send the xrdp process a SIGKILL signal.

# Answer
1. lsof -c xrdp | grep -v xrdp-sesman |grep / > /opt/quiz41/answers/file_audit.txt
2. lsof -i -P -n | grep 'xrdp' | cut -d: -f2 | awk '{print $1}'
3. ps aux | grep xrdp
   kill -SIGKILL 5917
