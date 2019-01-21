powershell检验SHA-256文件哈希
$algorithm = [Security.Cryptography.HashAlgorithm]:: Create(“SHA256”)
$vboxinstaller ='C:\Users\w4sp\Downloads\VirtualBox-5.0.4-102546-Win.exe'
$fileBytes = [io.File]:: ReadAllBytes($vboxinstaller)
$bytes = $algorithm.ComputeHash($fileBytes)
-Join($bytes | ForEach {“{0:x2}” -f $_}) 

w4sp-lab（VMware虚拟机安装）
https://github.com/w4sp-book/w4sp-lab/ 获取w4sp-lab实验室
unzip w4sp-lab-master.zip
python w4sp_webapp.py

出现的问题：
在加载文件时，python可能出现 ‘ascii’ codec can’t decode byte 0xe4 in position 0: ordinal not in range(128)的问题。对应不同版本的python，有不同的解决方案。

python2 
Python在进行编码方式之间的转换时，会将 unicode 作为“中间编码”，但 unicode 最大只有128那么长，所以这里当尝试将 ascii 编码字符串转换成”中间编码” unicode 时由于超出了其范围，就报出了如上错误。将Python的默认编码方式修改为utf-8即可，在py文件开头加入以下代码：
1.import sys
2.reload(sys) 
3.sys.setdefaultencoding('utf-8')

python3 
在python3中，sys.setdefaultencoding(‘utf-8’)已被禁用，将导入文件代码加上encoding=’bytes’则可解决：
x = pickle.load(open("./data/coco/word2vec.p","rb"), encoding='bytes')


