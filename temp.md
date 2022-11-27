74  watch -n 2 ./migrate.sh status
   75  ls -al
   76  ll
   77  docker-compose --env-file compose-env  --profile api up -d
   78  vi docker-compose.yml
   79  docker-compose --env-file compose-env  --profile api up -d
   80  docker-compose --env-file compose-env  --profile middleware up -d
   81  curl localhost:8080
   82  curl localhost:3000
   83  curl localhost:8080
   84  curl localhost:3000
   85  ls
   86  ll
   87  mysql -h $DB_HOST -u $DB_USERNAME -p
   88  ls
   89  ll
   90  curl https://querypie-api.kurlypay.co.kr
   91  ls
   92  tail -f /var/log/querypie/middleware/middleware.log
   93  ls
   94  ll
   95  tail -f /var/log/querypie/api/API.log
   96  hostname
   97  sudo vi /etc/hosts
   98  history | grep api
   99  docker-compose --env-file compose-env  --profile api up -d
  100  tail -f /var/log/querypie/api/API.log
  101  curl https://querypie-proxy.kurlypay.co.kr:6000
  102  curl -kv https://querypie-proxy.kurlypay.co.kr:6000
  103  clear
  104  sudo setenforce 0
  105  curl -L https://dl.querypie.com/releases/9.8/setup.sh -o setup.sh
  106  chmod +x setup.sh
  107  export INSTALL_TYPE=poc; ./setup.sh
  108  cd querypie
  109  sudo su -
  110  docker login harbor.chequer.io


Springframework.beans.factory.BeanCreationException: Error creating bean with name 'org.springframework.transaction.annotation.ProxyTransactionManagementConfiguration': Initialization of bean failed; nested exception is org.springframework.beans.factory.NoSuchBeanDefinitionException: No bean named 'org.springframework.context.annotation.ConfigurationClassPostProcessor.importRegistry' available
	at org.springframework.boot.context.logging.LoggingApplicationListener.initializeSystem(LoggingApplicationListener.java:327)
	at org.springframework.boot.context.logging.LoggingApplicationListener.initialize(LoggingApplicationListener.java:281)
	at org.springframework.boot.context.logging.LoggingApplicationListener.onApplicationEnvironmentPreparedEvent(LoggingApplicationListener.java:239)
	at org.springframework.boot.context.logging.LoggingApplicationListener.onApplicationEvent(LoggingApplicationListener.java:216)
	at org.springframework.context.event.SimpleApplicationEventMulticaster.doInvokeListener(SimpleApplicationEventMulticaster.java:176)
	at org.springframework.context.event.SimpleApplicationEventMulticaster.invokeListener(SimpleApplicationEventMulticaster.java:169)
	at org.springframework.context.event.SimpleApplicationEventMulticaster.multicastEvent(SimpleApplicationEventMulticaster.java:143)
	at org.springframework.context.event.SimpleApplicationEventMulticaster.multicastEvent(SimpleApplicationEventMulticaster.java:131)
	at org.springframework.boot.context.event.EventPublishingRunListener.environmentPrepared(EventPublishingRunListener.java:82)
	at org.springframework.boot.SpringApplicationRunListeners.lambda$environmentPrepared$2(SpringApplicationRunListeners.java:63)
	at java.base/java.util.ArrayList.forEach(ArrayList.java:1541)
	at org.springframework.boot.SpringApplicationRunListeners.doWithListeners(SpringApplicationRunListeners.java:117)
	at org.springframework.boot.SpringApplicationRunListeners.doWithListeners(SpringApplicationRunListeners.java:111)
	at org.springframework.boot.SpringApplicationRunListeners.environmentPrepared(SpringApplicationRunListeners.java:62)
	at org.springframework.boot.SpringApplication.prepareEnvironment(SpringApplication.java:362)
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:320)
	at org.springframework.boot.builder.SpringApplicationBuilder.run(SpringApplicationBuilder.java:144)
	at com.chequer.querypie.ApiApplication.main(ApiApplication.java:73)
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.base/java.lang.reflect.Method.invoke(Method.java:566)
	at org.springframework.boot.loader.MainMethodRunner.run(MainMethodRunner.java:49)
	at org.springframework.boot.loader.Launcher.launch(Launcher.java:107)
	at org.springframework.boot.loader.Launcher.launch(Launcher.java:58)
	at org.springframework.boot.loader.JarLauncher.main(JarLauncher.java:88)
Caused by: java.lang.IllegalStateException: Logback configuration error detected: