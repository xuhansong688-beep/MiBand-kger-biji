wait（I）得到子进程的PID，I是这个子进程的退出码，即exit（）里面的内容
`WEXITSTATUS(value)` 用于从 `value` 中提取子进程正常退出时的退出码
fork以后，出现的进程同时进行
信号kill以后，pause才能继续向下
