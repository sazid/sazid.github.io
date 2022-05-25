---
---

`du -sh file_path`

**Explanation**

 - `du` (**d**isc **u**sage) command estimates file_path space usage 
 - The options `-sh` are (from `man du`):
   
          -s, --summarize
                 display only a total for each argument
   
          -h, --human-readable
                 print sizes in human readable format (e.g., 1K 234M 2G)
   
   
   To check more than one directory and see the total, use `du -sch`:
   
          -c, --total
                 produce a grand total

Taken from: https://unix.stackexchange.com/a/185765
