# Quiz 1.1: Working with File Content
1. Extract all lines from the file /var/log/app-prod.log that contain the string reconnecting at the very end of the line only, and write them to the file /opt/quiz21/answers/output_a.log

2. Extract all lines from the file /var/log/app-prod.log that contain the string Terminating, and write them to the file /opt/quiz21/answers/output_b.log

3. Extract all lines from the file /var/log/app-prod.log that contain the string ERROR at the very start of the line only, and write them to the file /opt/quiz21/answers/output_c.log

# Answer
1. cat /var/log/app-prod.log | grep reconnecting$ > /opt/quiz21/answers/output_a.log
2. cat /var/log/app-prod.log | grep "Terminating" > /opt/quiz21/answers/output_b.log
3. cat /var/log/app-prod.log | grep ^ERROR > /opt/quiz21/answers/output_c.log

