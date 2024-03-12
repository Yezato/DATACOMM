# Quiz 6.1: Query The Package Management Database
Query the package management database on your system, and write the full package name only of the package which owns the binary /usr/bin/ssh-keygen to the file /opt/quiz71/package_output.txt which has already been created for your convenience.

# Answer
dpkg -S /usr/bin/ssh-keygen | cut -d: -f1 > /opt/quiz71/package_output.txt
