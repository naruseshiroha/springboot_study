# SpringBoot

## 配置文件优先级

`./config/child/application.yml> ./config/application.yml > ./application.yml > classpath:config/application.yml > classpath:application.yml`

## Web 开发

### 静态资源处理

```java
@Override
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    if (!this.resourceProperties.isAddMappings()) {
        logger.debug("Default resource handling disabled");
        return;
    }
    addResourceHandler(registry, "/webjars/**", "classpath:/META-INF/resources/webjars/");
    addResourceHandler(registry, this.mvcProperties.getStaticPathPattern(), (registration) -> {
        registration.addResourceLocations(this.resourceProperties.getStaticLocations());
        if (this.servletContext != null) {
            ServletContextResource resource = new ServletContextResource(this.servletContext, SERVLET_LOCATION);
            registration.addResourceLocations(resource);
        }
    });
}

private void addResourceHandler(ResourceHandlerRegistry registry, String pattern, String... locations) {
    addResourceHandler(registry, pattern, (registration) -> registration.addResourceLocations(locations));
}

private void addResourceHandler(ResourceHandlerRegistry registry, String pattern,
        Consumer<ResourceHandlerRegistration> customizer) {
    if (registry.hasMappingForPattern(pattern)) {
        return;
    }
    ResourceHandlerRegistration registration = registry.addResourceHandler(pattern);
    customizer.accept(registration);
    registration.setCachePeriod(getSeconds(this.resourceProperties.getCache().getPeriod()));
    registration.setCacheControl(this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl());
    registration.setUseLastModified(this.resourceProperties.getCache().isUseLastModified());
    customizeResourceHandlerRegistration(registration);
}

private void customizeResourceHandlerRegistration(ResourceHandlerRegistration registration) {
    if (this.resourceHandlerRegistrationCustomizer != null) {
        this.resourceHandlerRegistrationCustomizer.customize(registration);
    }
}


@ConfigurationProperties("spring.web")
public class WebProperties {
    // ...
    public static class Resources {
        private static final String[] CLASSPATH_RESOURCE_LOCATIONS = { "classpath:/META-INF/resources/",
                    "classpath:/resources/", "classpath:/static/", "classpath:/public/" };
    }
}
```

> 优先顺序: `classpath:/META-INF/resources/** > classpath:/resources/** > classpath:/static/** > classpath:/public/**`

### 首页

### Thymeleaf

### 扩展 Spring MVC

### 基本 CRUD

### 拦截器

### 国际化
