---

---

## 配置文件

### spring.config.additional-location 

不会使项目默认的配置文件失效，使用该命令行参数添加的外部配置文件会与项目默认的配置文件共同生效，形成互补配置，且其优先级是最高的，比所有默认配置文件的优先级都高。

### spring.config.location

使用该参数指定配置文件后，会使项目默认配置文件（application.properties 或 application.yml ）失效，Spring Boot 将只加载指定的外部配置文件。