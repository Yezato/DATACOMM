# Quiz .3: Comparing
1. Compare the content of the files /opt/quiz23/resources/file_d.txt and /opt/quiz23/resources/file_1.txt, ignoring case in your comparison. Redirect the output from your comparison on the file /opt/quiz23/answers/result_file.txt, which has already been created

2. Compare all file in directory (included subdirectory) /opt/quiz23/resources/file_dir and /opt/quiz23/resources/file_dir2. Write the absolute path of the different file to the output file /opt/quiz23/answers/differences_file.txt, which already exists

3. Two directories of the files are present - /opt/quiz23/resources/compare_c and /opt/quiz23/resources/compare_2, write a list of filenames only (do not include the path) of files which are only present in /opt/quiz23/resources/compare_c to /opt/quiz23/answers/differences_filenames_only.txt

# Answer
```lua
1. diff -i /opt/quiz23/resources/file_d.txt /opt/quiz23/resources/file_1.txt > /opt/quiz23/answers/result_file.txt
```
```lua
2. diff -r /opt/quiz23/resources/file_dir /opt/quiz23/resources/file_dir2 > /opt/quiz23/answers/differences_file.txt
```
```lua
3. diff -r /opt/quiz23/resources/compare_c /opt/quiz23/resources/compare_2 | grep compare_c | awk '{print $4}' > /opt/quiz23/answers/differences_filenames_only.txt
```
