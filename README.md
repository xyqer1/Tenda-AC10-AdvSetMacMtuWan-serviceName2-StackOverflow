# web_aaa_enableAuthlistEdit_get-authName-StackOverflow

﻿During my internship at Qi An Xin Tiangong Lab, I discovered a  stack overflow vulnerability in the Planet router.

By analyzing the dispatcher file in the bin directory, I found that the function web_aaa_enableAuthlistEdit_get contains a stack overflow vulnerability.

The stack overflow can be triggered by the authName key value, which leads to a strcpy stack overflow.

![image-20250326111931692](https://gitee.com/xyqer/pic/raw/master/202503261119743.png)

In the main function, there is an account authentication detection. We create a cookie_0 in the tmp directory, with the content of "20 0 0", and its function is to create a cookie with sufficient permissions to access this route.

## How can we simulate a router

﻿Use the following command to simulate with qemu.

```bash
sudo chroot . ./qemu-mips-static -E REQUEST_METHOD=POST -E HTTP_COOKIE='hid=0' -L ./lib ./dispatcher.cgi < poc
```

﻿The content of the **poc** file is as follows:

```json
&cmd=6919&authName=aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
```

## Attack result

![image-20250331103415881](https://gitee.com/xyqer/pic/raw/master/202503311034901.png)

Through IDA, it can be seen that the stack space is 0x58.

![image-20250331103354443](https://gitee.com/xyqer/pic/raw/master/202503311033513.png)

![image-20250331103405576](https://gitee.com/xyqer/pic/raw/master/202503311034655.png)

Through the above image, we can see that we have overflowed to 0x80 and successfully hijacked the control flow. If necessary, more can be overflowed.