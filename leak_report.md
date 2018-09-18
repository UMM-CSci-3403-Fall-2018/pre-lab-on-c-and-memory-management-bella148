# Leak report

The output log showed:

46 bytes in 6 blocks are definitely lost in loss record 1 of 1
at 0x4C2FA50: calloc(vg_replace_malloc.c:711)
by 0x400678: strip (check_whitespace.c:41)
by 0x4006E3: is_clean (check_whitespace.c:62)
by 0x40076B: main (check_whitespace.c:87)

What this means is that the memory allocated by calloc in strip (which in turn was called by is_clean and main) was never freed. In strip, because the data we need is being returned,
we are not able to remove it quiet yet. However, in is_clean, there is a simple test to compare string lengths (strcmp). After the result of the command, the memory allocated for cleaned,
or result in strip(), is no longer used and it is available to be freed. There is an issue with a NULL pointer though, which will cause an error to happen if we try to free memory that
is null. To prevent this, we check if the null terminator is the first element in the memory, and if it is, then we are not safe to free that memory since it is not taking up space.