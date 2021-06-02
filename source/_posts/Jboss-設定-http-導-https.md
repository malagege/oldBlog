---
title: Jboss 設定 http 導 https
date: 2021-06-02 20:48:24
tags: [jboss,http,https]
categories: JBoss
---


<!--more-->

## redirect

https://stackoverflow.com/a/43948374


我有測試上面網站方式，本機測試可以http導向https
我這邊standalone.xml 剛好預設有那些https設定，所以沒調整
project /WebContent/WEB-INF/web.xml 也需要加


```xml
<security-constraint>
    <web-resource-collection>
        <web-resource-name>WEB_APPLICATION_NAME</web-resource-name>
        <url-pattern>/*</url-pattern>
    </web-resource-collection>
    <user-data-constraint>
        <transport-guarantee>CONFIDENTIAL</transport-guarantee>
    </user-data-constraint>
</security-constraint>
```
參考:https://www.cnblogs.com/maximo/p/5775627.html



## rewrite 
standalone.xml
```xml
        <subsystem xmlns="urn:jboss:domain:transactions:4.0">
            <core-environment>
                <process-id>
                    <uuid/>
               </process-id>
            </core-environment>
            <recovery-environment socket-binding="txn-recovery-environment" status-socket-binding="txn-status-manager"/>
            <object-store path="tx-object-store" relative-to="jboss.server.data.dir"/>
        </subsystem>
        <subsystem xmlns="urn:jboss:domain:undertow:4.0">
            <buffer-cache name="default"/>
            <server name="default-server">
                <http-listener name="default" socket-binding="http" redirect-socket="https" enable-http2="true"/>
                <https-listener name="https" socket-binding="https" security-realm="ApplicationRealm" enable-http2="true"/>
                <host name="default-host" alias="localhost">
                    <location name="/" handler="welcome-content"/>
                    <filter-ref name="server-header"/>
                    <filter-ref name="x-powered-by-header"/>
                    <http-invoker security-realm="ApplicationRealm"/>
                    <filter-ref name="http-to-https" predicate="equals(%p,8080)"/>
                </host>
            </server>
            <servlet-container name="default">
                <jsp-config development="true"/>
                <websockets/>
            </servlet-container>
            <handlers>
                <file name="welcome-content" path="${jboss.home.dir}/welcome-content"/>
            </handlers>
            <filters>
                <response-header name="server-header" header-name="Server" header-value="JBoss-EAP/7"/>
                <response-header name="x-powered-by-header" header-name="X-Powered-By" header-value="Undertow/1"/>
                <rewrite name="http-to-https" redirect="true" target="https://%h:8443%U"/>
            </filters>
        </subsystem>
```
