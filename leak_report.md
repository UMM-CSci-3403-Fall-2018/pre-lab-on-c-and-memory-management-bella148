# Leak report

The output log showed:

46 bytes in 6 blocks are definitely lost in loss record 1 of 1
at 0x4C2FA50: calloc(vg_replace_malloc.c:711)
by 0x400678: strip (check_whitespace.c:41)
by 0x4006E3: is_clean (check_whitespace.c:62)
by 0x40076B: main (check_whitespace.c:87)

This means the function calloc was the cause of the memory leak called by strip called by is_clean called by main. This is because calloc is responsible for allocating memory that we use to store variable values, and that memory is never freed before returning the results, making it is_clean's job to free the memory.