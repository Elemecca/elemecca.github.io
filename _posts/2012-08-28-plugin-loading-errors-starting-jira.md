---
title: Plugin Loading Errors Starting JIRA 
date: 2012-08-28 17:19:37.000000000 -07:00
tags:
- troubleshooting
- atlas-run
- atlassian-plugin-sdk
- Java
- JIRA
- jira-importers
---

Based on the discussion in [JIM-254] I'm extending the [JIRA
Importers Plugin][JIM] (JIM) to support [RT]. I was trying to start
JIRA for testing with `atlas-run -DskipTests`, but after the initial
setup process it failed to load with this error message:


```none
[INFO] [talledLocalContainer] 2012-08-28 15:45:19,431 main FATAL      [atlassian.jira.startup.JiraStartupLogger] 
[INFO] [talledLocalContainer] 
[INFO] [talledLocalContainer] ********************************************************************************************************************************************************************************************************
[INFO] [talledLocalContainer] The following plugins are required by JIRA, but have not been started: Atlassian JIRA - Plugins - Project Config Plugin (com.atlassian.jira.jira-project-config-plugin), Atlassian OAuth Admin Plugin (com.atlassian.oauth.admin), Embedded Gadgets Plugin (com.atlassian.gadgets.embedded), Gadget Dashboard Plugin (com.atlassian.gadgets.dashboard), Atlassian JIRA - Plugins - Look And Feel Logo Upload Plugin (com.atlassian.jira.lookandfeel), Atlassian REST - Module Types (com.atlassian.plugins.rest.atlassian-rest-module), Gadget Spec Publisher Plugin (com.atlassian.gadgets.publisher), Atlassian JIRA - Plugins - REST Plugin (com.atlassian.jira.rest), Atlassian OAuth Service Provider Plugin (com.atlassian.oauth.serviceprovider), Atlassian OAuth Consumer Plugin (com.atlassian.oauth.consumer), Opensocial Plugin (com.atlassian.gadgets.opensocial), Gadget Directory Plugin (com.atlassian.gadgets.directory), Atlassian JIRA - Plugins - SAL Plugin (com.atlassian.sal.jira), Atlassian JIRA - Plugins - Gadgets Plugin (com.atlassian.jira.gadgets)
[INFO] [talledLocalContainer] ********************************************************************************************************************************************************************************************************
```

That error is very generic, and just means that something went wrong
somewhere in the plugin initialization process. A few hundred lines up
from that I found the root cause:

```none
[INFO] [talledLocalContainer] 2012-08-28 15:43:07,838 Spring executor 5 ERROR      [plugin.osgi.factory.OsgiPlugin] Unable to start the Spring context for plugin com.atlassian.jira.plugins.jira-importers-plugin
[INFO] [talledLocalContainer] org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'pivotalRapidBoardManager' defined in URL [bundle://55.0:0/META-INF/spring/atlassian-plugins-components.xml]: Initialization of bean failed; nested exception is java.lang.TypeNotPresentException: Type com.atlassian.greenhopper.api.rapid.view.Column not present
[INFO] [talledLocalContainer]   at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:480)
[INFO] [talledLocalContainer]   at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory$1.run(AbstractAutowireCapableBeanFactory.java:409)
[INFO] [talledLocalContainer]   at java.security.AccessController.doPrivileged(Native Method)
[INFO] [talledLocalContainer]   at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:380)
[INFO] [talledLocalContainer]   at org.springframework.beans.factory.support.AbstractBeanFactory$1.getObject(AbstractBeanFactory.java:264)
[INFO] [talledLocalContainer]   at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:222)
[INFO] [talledLocalContainer]   at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:261)
[INFO] [talledLocalContainer]   at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:185)
[INFO] [talledLocalContainer]   at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:164)
[INFO] [talledLocalContainer]   at org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingletons(DefaultListableBeanFactory.java:429)
[INFO] [talledLocalContainer]   at org.springframework.context.support.AbstractApplicationContext.finishBeanFactoryInitialization(AbstractApplicationContext.java:728)
[INFO] [talledLocalContainer]   at org.springframework.osgi.context.support.AbstractDelegatedExecutionApplicationContext.access$1600(AbstractDelegatedExecutionApplicationContext.java:69)
[INFO] [talledLocalContainer]   at org.springframework.osgi.context.support.AbstractDelegatedExecutionApplicationContext$4.run(AbstractDelegatedExecutionApplicationContext.java:355)
[INFO] [talledLocalContainer]   at org.springframework.osgi.util.internal.PrivilegedUtils.executeWithCustomTCCL(PrivilegedUtils.java:85)
[INFO] [talledLocalContainer]   at org.springframework.osgi.context.support.AbstractDelegatedExecutionApplicationContext.completeRefresh(AbstractDelegatedExecutionApplicationContext.java:320)
[INFO] [talledLocalContainer]   at org.springframework.osgi.extender.internal.dependencies.startup.DependencyWaiterApplicationContextExecutor$CompleteRefreshTask.run(DependencyWaiterApplicationContextExecutor.java:132)
[INFO] [talledLocalContainer]   at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1110)
[INFO] [talledLocalContainer]   at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:603)
[INFO] [talledLocalContainer]   at java.lang.Thread.run(Thread.java:722)
[INFO] [talledLocalContainer] Caused by: java.lang.TypeNotPresentException: Type com.atlassian.greenhopper.api.rapid.view.Column not present
[INFO] [talledLocalContainer]   at sun.reflect.generics.factory.CoreReflectionFactory.makeNamedType(CoreReflectionFactory.java:117)
[INFO] [talledLocalContainer]   at sun.reflect.generics.visitor.Reifier.visitClassTypeSignature(Reifier.java:125)
[INFO] [talledLocalContainer]   at sun.reflect.generics.tree.ClassTypeSignature.accept(ClassTypeSignature.java:49)
[INFO] [talledLocalContainer]   at sun.reflect.generics.visitor.Reifier.reifyTypeArguments(Reifier.java:68)
[INFO] [talledLocalContainer]   at sun.reflect.generics.visitor.Reifier.visitClassTypeSignature(Reifier.java:138)
[INFO] [talledLocalContainer]   at sun.reflect.generics.tree.ClassTypeSignature.accept(ClassTypeSignature.java:49)
[INFO] [talledLocalContainer]   at sun.reflect.generics.repository.MethodRepository.getReturnType(MethodRepository.java:68)
[INFO] [talledLocalContainer]   at java.lang.reflect.Method.getGenericReturnType(Method.java:244)
[INFO] [talledLocalContainer]   at java.beans.FeatureDescriptor.getReturnType(FeatureDescriptor.java:370)
[INFO] [talledLocalContainer]   at java.beans.Introspector.getTargetEventInfo(Introspector.java:974)
[INFO] [talledLocalContainer]   at java.beans.Introspector.getBeanInfo(Introspector.java:434)
[INFO] [talledLocalContainer]   at java.beans.Introspector.getBeanInfo(Introspector.java:174)
[INFO] [talledLocalContainer]   at org.springframework.beans.CachedIntrospectionResults.(CachedIntrospectionResults.java:220)
[INFO] [talledLocalContainer]   at org.springframework.beans.CachedIntrospectionResults.forClass(CachedIntrospectionResults.java:144)
[INFO] [talledLocalContainer]   at org.springframework.beans.BeanWrapperImpl.getCachedIntrospectionResults(BeanWrapperImpl.java:252)
[INFO] [talledLocalContainer]   at org.springframework.beans.BeanWrapperImpl.getPropertyDescriptors(BeanWrapperImpl.java:259)
[INFO] [talledLocalContainer]   at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.filterPropertyDescriptorsForDependencyCheck(AbstractAutowireCapableBeanFactory.java:1132)
[INFO] [talledLocalContainer]   at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.populateBean(AbstractAutowireCapableBeanFactory.java:992)
[INFO] [talledLocalContainer]   at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:472)
[INFO] [talledLocalContainer]   ... 18 more
[INFO] [talledLocalContainer] Caused by: java.lang.ClassNotFoundException: com.atlassian.greenhopper.api.rapid.view.Column
[INFO] [talledLocalContainer]   at org.apache.felix.framework.ModuleImpl.findClassOrResourceByDelegation(ModuleImpl.java:772)
[INFO] [talledLocalContainer]   at org.apache.felix.framework.ModuleImpl.access$200(ModuleImpl.java:73)
[INFO] [talledLocalContainer]   at org.apache.felix.framework.ModuleImpl$ModuleClassLoader.loadClass(ModuleImpl.java:1690)
[INFO] [talledLocalContainer]   at java.lang.ClassLoader.loadClass(ClassLoader.java:356)
[INFO] [talledLocalContainer]   at java.lang.Class.forName0(Native Method)
[INFO] [talledLocalContainer]   at java.lang.Class.forName(Class.java:264)
[INFO] [talledLocalContainer]   at sun.reflect.generics.factory.CoreReflectionFactory.makeNamedType(CoreReflectionFactory.java:114)
[INFO] [talledLocalContainer]   ... 36 more
```

That error makes it fairly clear that JIM depends on GreenHopper, which
isn't being loaded. That theory is borne out by JIM's POM, which
declares a runtime dependency on GreenHopper. I therefore tried adding
GreenHopper by calling
`atlas-run -DskipTests --plugins com.atlassian.jira.plugins:jira-greenhopper-plugin`,
but that failed as well with the same error. After poring over the new
log, I found this error message:

```none
[INFO] [talledLocalContainer] 2012-08-28 16:17:50,528 main ERROR      [plugin.osgi.factory.OsgiPlugin] Detected an error (BundleException) enabling the plugin 'com.pyxis.greenhopper.jira' : Unresolved constraint in bundle com.pyxis.greenhopper.jira [96]: Unable to resolve 96.0: missing requirement [96.0] package; (&(package=javax.xml.parsers)(version>=0.0.0.1_006_JavaSE)(version<=0.0.0.1_006_JavaSE)).  This error usually occurs when your plugin imports a package from another bundle with a specific version constraint and either the bundle providing that package doesn't meet those version constraints, or there is no bundle available that provides the specified package. For more details on how to fix this, see http://confluence.atlassian.com/x/1xy6D
[INFO] [talledLocalContainer] 2012-08-28 16:17:50,529 main WARN      [plugin.osgi.factory.OsgiPlugin] Unable to enable plugin 'com.pyxis.greenhopper.jira'
[INFO] [talledLocalContainer] com.atlassian.plugin.osgi.container.OsgiContainerException: Cannot start plugin: com.pyxis.greenhopper.jira
[INFO] [talledLocalContainer]   at com.atlassian.plugin.osgi.factory.OsgiPlugin.enableInternal(OsgiPlugin.java:445)
[INFO] [talledLocalContainer]   at com.atlassian.plugin.impl.AbstractPlugin.enable(AbstractPlugin.java:237)
[INFO] [talledLocalContainer]   at com.atlassian.plugin.manager.PluginEnabler.actualEnable(PluginEnabler.java:114)
[INFO] [talledLocalContainer]   at com.atlassian.plugin.manager.PluginEnabler.enable(PluginEnabler.java:99)
[INFO] [talledLocalContainer]   at com.atlassian.plugin.manager.DefaultPluginManager.addPlugins(DefaultPluginManager.java:713)
[INFO] [talledLocalContainer]   at com.atlassian.plugin.manager.DefaultPluginManager.init(DefaultPluginManager.java:166)
[INFO] [talledLocalContainer]   at com.atlassian.jira.plugin.JiraPluginManager.start(JiraPluginManager.java:58)
[INFO] [talledLocalContainer]   at com.atlassian.jira.ComponentManager$PluginSystem.start(ComponentManager.java:681)
[INFO] [talledLocalContainer]   at com.atlassian.jira.ComponentManager.startPluginSystem(ComponentManager.java:240)
[INFO] [talledLocalContainer]   at com.atlassian.jira.ComponentManager.quickStart(ComponentManager.java:233)
[INFO] [talledLocalContainer]   at com.atlassian.jira.ComponentManager.start(ComponentManager.java:218)
[INFO] [talledLocalContainer]   at com.atlassian.jira.upgrade.PluginSystemLauncher.start(PluginSystemLauncher.java:23)
[INFO] [talledLocalContainer]   at com.atlassian.jira.startup.DefaultJiraLauncher$3.run(DefaultJiraLauncher.java:101)
[INFO] [talledLocalContainer]   at com.atlassian.jira.config.database.DatabaseConfigurationManagerImpl.doNowOrEnqueue(DatabaseConfigurationManagerImpl.java:250)
[INFO] [talledLocalContainer]   at com.atlassian.jira.config.database.DatabaseConfigurationManagerImpl.doNowOrWhenDatabaseActivated(DatabaseConfigurationManagerImpl.java:149)
[INFO] [talledLocalContainer]   at com.atlassian.jira.startup.DefaultJiraLauncher.postDbLaunch(DefaultJiraLauncher.java:94)
[INFO] [talledLocalContainer]   at com.atlassian.jira.startup.DefaultJiraLauncher.access$100(DefaultJiraLauncher.java:24)
[INFO] [talledLocalContainer]   at com.atlassian.jira.startup.DefaultJiraLauncher$1.run(DefaultJiraLauncher.java:61)
[INFO] [talledLocalContainer]   at com.atlassian.jira.util.devspeed.JiraDevSpeedTimer.run(JiraDevSpeedTimer.java:33)
[INFO] [talledLocalContainer]   at com.atlassian.jira.startup.DefaultJiraLauncher.start(DefaultJiraLauncher.java:56)
[INFO] [talledLocalContainer]   at com.atlassian.jira.startup.LauncherContextListener$1.create(LauncherContextListener.java:67)
[INFO] [talledLocalContainer]   at com.atlassian.jira.startup.LauncherContextListener$1.create(LauncherContextListener.java:62)
[INFO] [talledLocalContainer]   at com.atlassian.multitenant.impl.MultiTenantComponentMapImpl.get(MultiTenantComponentMapImpl.java:121)
[INFO] [talledLocalContainer]   at com.atlassian.multitenant.impl.MultiTenantComponentMapImpl.onTenantStart(MultiTenantComponentMapImpl.java:165)
[INFO] [talledLocalContainer]   at com.atlassian.multitenant.impl.DefaultMultiTenantManager$1.consume(DefaultMultiTenantManager.java:134)
[INFO] [talledLocalContainer]   at com.atlassian.multitenant.impl.DefaultMultiTenantManager$1.consume(DefaultMultiTenantManager.java:131)
[INFO] [talledLocalContainer]   at com.atlassian.multitenant.impl.DefaultMultiTenantManager.runForEachListener(DefaultMultiTenantManager.java:256)
[INFO] [talledLocalContainer]   at com.atlassian.multitenant.impl.DefaultMultiTenantManager.startTenant(DefaultMultiTenantManager.java:130)
[INFO] [talledLocalContainer]   at com.atlassian.multitenant.impl.DefaultMultiTenantManager.startAll(DefaultMultiTenantManager.java:203)
[INFO] [talledLocalContainer]   at com.atlassian.jira.startup.LauncherContextListener.contextInitialized(LauncherContextListener.java:95)
[INFO] [talledLocalContainer]   at org.apache.catalina.core.StandardContext.listenerStart(StandardContext.java:3934)
[INFO] [talledLocalContainer]   at org.apache.catalina.core.StandardContext.start(StandardContext.java:4429)
[INFO] [talledLocalContainer]   at org.apache.catalina.core.ContainerBase.start(ContainerBase.java:1045)
[INFO] [talledLocalContainer]   at org.apache.catalina.core.StandardHost.start(StandardHost.java:722)
[INFO] [talledLocalContainer]   at org.apache.catalina.core.ContainerBase.start(ContainerBase.java:1045)
[INFO] [talledLocalContainer]   at org.apache.catalina.core.StandardEngine.start(StandardEngine.java:443)
[INFO] [talledLocalContainer]   at org.apache.catalina.core.StandardService.start(StandardService.java:516)
[INFO] [talledLocalContainer]   at org.apache.catalina.core.StandardServer.start(StandardServer.java:710)
[INFO] [talledLocalContainer]   at org.apache.catalina.startup.Catalina.start(Catalina.java:583)
[INFO] [talledLocalContainer]   at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
[INFO] [talledLocalContainer]   at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
[INFO] [talledLocalContainer]   at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
[INFO] [talledLocalContainer]   at java.lang.reflect.Method.invoke(Method.java:601)
[INFO] [talledLocalContainer]   at org.apache.catalina.startup.Bootstrap.start(Bootstrap.java:288)
[INFO] [talledLocalContainer]   at org.apache.catalina.startup.Bootstrap.main(Bootstrap.java:413)
[INFO] [talledLocalContainer] Caused by: org.osgi.framework.BundleException: Unresolved constraint in bundle com.pyxis.greenhopper.jira [96]: Unable to resolve 96.0: missing requirement [96.0] package; (&(package=javax.xml.parsers)(version>=0.0.0.1_006_JavaSE)(version<=0.0.0.1_006_JavaSE))
[INFO] [talledLocalContainer]   at org.apache.felix.framework.Felix.resolveBundle(Felix.java:3409)
[INFO] [talledLocalContainer]   at org.apache.felix.framework.Felix.startBundle(Felix.java:1709)
[INFO] [talledLocalContainer]   at org.apache.felix.framework.BundleImpl.start(BundleImpl.java:905)
[INFO] [talledLocalContainer]   at org.apache.felix.framework.BundleImpl.start(BundleImpl.java:892)
[INFO] [talledLocalContainer]   at com.atlassian.plugin.osgi.factory.OsgiPlugin.enableInternal(OsgiPlugin.java:417)
[INFO] [talledLocalContainer]   ... 44 more
```

That, finally, was the clue I needed. GreenHopper depends on
`javax.xml.parsers`, which is provided by the JRE. The version number
means it expects the version provided by Java SE 6. As it happens, my
default Java runtime is JDK 1.7.0. I switched to JDK 1.6.0\_27 for
running JIRA and now it starts just fine.

Also, explicitly requesting GreenHopper on the `atlas-run` command line
is unnecessary. Now that I'm using the right JRE GreenHopper loads
automatically, presumably based on the dependency in JIM's POM. I'm not
sure why the dependency error didn't show up until I requested
GreenHopper explicitly.

[JIM-254]: https://studio.atlassian.com/browse/JIM-254?focusedCommentId=52269#comment-52269 "Atlassian issue JIM-254"
[JIM]: https://marketplace.atlassian.com/plugins/com.atlassian.jira.plugins.jira-importers-plugin "JIM in the Atlassian Marketplace"
[RT]: http://www.bestpractical.com/rt/ "Request Tracker by Best Practical"
