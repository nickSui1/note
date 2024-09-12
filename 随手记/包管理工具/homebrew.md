### homebrew
 - brew --prefix
 <br/> 用于标识homebrew的base目录，To find out which base directory Homebrew is using on your machine, run brew --prefix.
  <br/>	用例：`tail $(brew --prefix)/var/log/mongodb/mongo.log` <br/>
  表明，显示homebrew根目录下的/var/log/mongodb/mongo.log文件的最后十行日志。<br/>
  注：$() 类似于``转义，称为command substitution（命令替换）</br>
 - 指令执行速度慢，出现</br>
  Downloading https://formulae.brew.sh/api/formula.jws.json 的时候，执行一下指令：</br>
  ` export HOMEBREW_NO_INSTALL_FROM_API=1 ` 
  </br>就可以解决，执行后可以用 ` brew config ` 查看是否设置成功。