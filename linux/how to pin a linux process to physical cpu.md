#### 如何将linux的进程绑定的物理的CPU


这里我们利用htop命令可以很方便的办到

* 首先我们对htop进行一些简单的设置。 这样我们展示出*进程使用的CPU* 

   
   1> 在htop界面按下"SHIFT + S ", 进入"Setup"界面， 然后选中 “Columns” --> "Available Columns"找到 "PROCESSOR" 列， 按下回车键。然后按ESC键回到显示页面

![images](.images/Screenshot from 2018-02-23 18-02-39.png)

   
   2> 这样显示页面我们就可以看到多了一个”CPU“的列， 这个就对应我们的"PROCESSOR", 表示"Id of the CPU the process last executed on". 
   ![](.images/Screenshot from 2018-02-23 18-13-42.png)
   
* 在显示界面下， 按下“a”界面， 可以设置进程pin的cpu情况。
  ![](.images/Screenshot from 2018-02-23 18-15-05.png)
  
* 在Use CPUs中我们可以通过按 空格键来选中或者取消CPU的绑定， 设置好后按下回车键确认。
  ![](.images/Screenshot from 2018-02-23 18-15-18.png)
  