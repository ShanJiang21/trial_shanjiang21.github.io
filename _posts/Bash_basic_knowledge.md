#### Making a bash script

```bash
touch file.name
echo
```

In order to run a file directly, we'll need to change the permissions to allow the script to be executable for the user. `chmod` is a command that changes permissions on a file, and `+x` will add execute rights to the script.

**Good old "top" command**: show the use of the CPU on your laptop, to quit using `q`

```bash
top
```



#### Hive报bug原因

- Distinct 不可以specify 多个field; Hive doesn't support `DISTINCT * ` syntax. You can manually specify every field of the table to get the same result:

  ```sql
  SELECT DISTINCT field1, field2, ...., fieldN
    FROM first_working_table
  ```



- from_unixtime from_unixtime(bigint unixtime[, string format]) string 转化UNIX时间戳（从1970-01-01 00:00:00 UTC到指定时间的秒数）到当前时区的时间格式

* The snippet of the stackoverflow: https://stackoverflow.blog/2014/09/16/introducing-runnable-javascript-css-and-html-code-snippets/


#### Fundamental ABCs in tech industry 

https://pdos.csail.mit.edu/6.S081/2020/
