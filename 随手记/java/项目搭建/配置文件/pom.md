### 创建项目的包引用以及pom配置
- 1，可以使用idea快速创建springboot项目，创建过程中进行勾选。
- 2，以上创建好项目后，如果还想引用其他的包，可以在pom中的dependencies节点下自行添加。

### 关于pom
- pom中的parent节点，spring-boot-starter-parent，提供了对版本的默认统一配置，如果配置了parent，那么deendency中的引用包就不需要配置版本号了。
- 如果需要指定版本号，可以在parent中配置
- 没有parent节点，需要在每个引用下指定版本号