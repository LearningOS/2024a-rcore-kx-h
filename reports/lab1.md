## 实现的功能
`get_start_time`获取开始时间
`get_syscall_times`获取调用时间
`accumulate_syscall_times`累加系统调用的次数

```rust
pub fn sys_task_info(_ti: *mut TaskInfo) -> isize {
    trace!("kernel: sys_task_info");
    unsafe {
        (*_ti).status = TaskStatus::Running;
        (*_ti).syscall_times = crate::task::TASK_MANAGER.get_syscall_times();
        (*_ti).time = crate::timer::get_time_ms() - crate::task::TASK_MANAGER.get_start_time();
    }
    0
}
```
在`sys_task_info`调用上述功能实现要求

## 简答题

正确进入 U 态后，程序的特征还应有：使用 S 态特权指令，访问 S 态寄存器后会报错。 请同学们可以自行测试这些内容（运行 三个 bad 测例 (ch2b_bad_*.rs) ）， 描述程序出错行为，同时注意注明你使用的 sbi 及其版本。

 RustSBI version 0.3.0-alpha.2
 报错信息：[kernel] PageFault in application, bad addr = 0x0, bad instruction = 0x804003a4, kernel killed it.
[kernel] IllegalInstruction in application, kernel killed it.
[kernel] IllegalInstruction in application, kernel killed it.
 深入理解 trap.S 中两个函数 __alltraps 和 __restore 的作用，并回答如下问题:

L40：刚进入 __restore 时，a0 代表了什么值。请指出 __restore 的两种使用情景。

a0中保存系统调用的参数

两种情形：内核态切换到用户态的时候，返回上下文；或者在应用程序切换的时候切换上下文



L43-L48：这几行汇编代码特殊处理了哪些寄存器？这些寄存器的的值对于进入用户态有何意义？请分别解释。

l43 : sstatus寄存器
l44 ： sepc寄存器
l45 ： sscrach寄存器
对于用户态的切换很重要，用于回复上下文信息

L50-L56：为何跳过了 x2 和 x4？
x2 是栈指针，已经指向内核栈
x4 多数情况下不必处理

L60：该指令之后，sp 和 sscratch 中的值分别有什么意义？
sp是用户栈指针，sscratch是内核栈指针

__restore：中发生状态切换在哪一条指令？为何该指令执行之后会进入用户态？
状态切换发生在 sret 指令

L13：该指令之后，sp 和 sscratch 中的值分别有什么意义？

csrrw sp, sscratch, sp
sp是内核栈指针，sscratch是用户栈指针

从 U 态进入 S 态是哪一条指令发生的？

csrrw sp, sscratch, sp实现了用户态向内核态转变

在完成本次实验的过程（含此前学习的过程）中，我曾分别与 以下各位 就（与本次实验相关的）以下方面做过交流，还在代码中对应的位置以注释形式记录了具体的交流对象及内容：

无

此外，我也参考了 以下资料 ，还在代码中对应的位置以注释形式记录了具体的参考来源及内容：

无