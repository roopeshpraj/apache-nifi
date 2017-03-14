# apache-nifi
apache-nifi-configuration

Niagra Files or Nifi was built to automate the flow of data between systems. 

<b>Download</b>
Download it from here  http://nifi.apache.org/download.html (latest version 1.1.2)

<b>Pre-requisites</b>
Requires Java 8 or newer

<b>Enabling HTTPS</b>
Create a keystore and a truststore 

Set the following in nifi.properties
nifi.web.https.host=nifidev.baplc.com
nifi.web.https.port=8443

nifi.security.keystore=/path/keystore.jks
nifi.security.keystoreType=JKS
nifi.security.keystorePasswd=MNO
nifi.security.keyPasswd=ABC
nifi.security.truststore=/path/truststore.jks
nifi.security.truststoreType=JKS
nifi.security.truststorePasswd=XYZ
nifi.security.needClientAuth=true
nifi.security.user.authorizer=file-provider
nifi.security.user.login.identity.provider=ldap-provider

Restart nifi, it should be available at https://nifiurl:8443/nifi

<b>LDAP Configuration</b>

Set the following in nifi.properties
nifi.security.identity.mapping.pattern.dn=^ou=(.*?),ou=(.*?),ou=(.*?),dc=(.*?),dc=(.*?)
nifi.security.identity.mapping.value.dn=$1@$2

Set the following in login-identity-providers.xml

<provider>
        <identifier>ldap-provider</identifier>
        <class>org.apache.nifi.ldap.LdapProvider</class>
        <property name="Authentication Strategy">SIMPLE</property>
        <property name="Manager DN">uid=XYZA,ou=systemids,ou=identities,ou=baplc,dc=ba,dc=com</property>
        <property name="Manager Password">MNOP</property>

        <property name="Referral Strategy">FOLLOW</property>
        <property name="Connect Timeout">10 secs</property>
        <property name="Read Timeout">10 secs</property>

        <property name="Url">ldap://googling.com:389</property>
        <property name="User Search Base">ou=employees,ou=identities,ou=baplc,dc=ba,dc=com</property>
        <property name="User Search Filter">(uid={0})</property>

        <property name="Identity Strategy">USE_DN</property>
        <property name="Authentication Expiration">12 hours</property>
    </provider>

Set the following in authorizers.xml

        <identifier>file-provider</identifier>
        <class>org.apache.nifi.authorization.FileAuthorizer</class>
        <property name="Authorizations File">./conf/authorizations.xml</property>
        <property name="Users File">./conf/users.xml</property>
        <property name="Initial Admin Identity"></property>
        <property name="Legacy Authorized Users File">./conf/authorized-users.xml</property>

Add users in authorized-users.xml
    <user dn="employeenumber=USER1,ou=employees,ou=identities,ou=baplc,dc=ba,dc=com">
        <role name="ROLE_ADMIN"/>
        <role name="ROLE_DFM"/>
    </user>

Delete users.xml and authorizations.xml and then restart nifi, https://nifiurl:8443/nifi should redirect you to https://nifiurl:8443/nifi/login login prompt. Upon successful login, you land upon Nifi admin console. 

