---
layout: post
title: "Lets Encrypt with a Kentico CMS Website"
date: 2018-02-10
tags: [iis, kentico]
---

Let's Encrypt is a really great, free SSL certificate issuer and manager, and there are a number of
third party programs that have been written to support it, including plugins for IIS.

One of the domain verification methods that Let's Encrypt uses is an extensionless file that must be
publicly accessible on the web, at the path `/.well-known/acme-challenge/<random-extensionless-file>`.
Normally this isn't a problem, but when you have a CMS that installs an HTTP module that routes *all traffic*
through itself, the domain verification will fail.

After toying with this for a few hours, I've come up with a solution that will work for most .NET-based CMSes,
and I will be providing example code that is specific to Kentico CMS, but can be adapted to other CMSes.

1. Convert the `.well-known` folder to an IIS sub-application.
2. Remove any modules that are added at the CMS level, and also remove any type references to membership/role
   providers or other types it may be trying to reference from the parent `web.config` in this /bin/ folder 
   (which is empty - your `.well-known` folder should not have a `/bin/` folder).
3. Create and add the following code to the `web.config` file in the `.well-known` folder (NOT the `acme-challenge`
   folder):
   
```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>

	<system.web>
		<authorization>
			<allow users="*" />
		</authorization>

		<roleManager enabled="false">
			<providers>
				<clear />
			</providers>
		</roleManager>
	</system.web>

  <system.webServer>
  
	<validation validateIntegratedModeConfiguration="false" />
    <staticContent>
		<remove fileExtension="." />
    </staticContent>
	
	<handlers>
		<clear />
		<add name="StaticFile" path="*" verb="*" type="" modules="StaticFileModule,DefaultDocumentModule,DirectoryListingModule" scriptProcessor="" resourceType="Either" requireAccess="Read" allowPathInfo="false" preCondition="" responseBufferLimit="4194304" />
	</handlers>
  
	<modules runAllManagedModulesForAllRequests="true">
		<remove name="WebDAVModule" />
		<remove name="XHtmlModule" />
		<remove name="CMSApplicationModule" />
	</modules>
  
    <!-- This will stop any redirects you have at the higher level -->
    <httpRedirect enabled="false" />
	
  </system.webServer>
  
</configuration>
```

Lastly, if you are using a managed Let's Encrypt application such as [Certify the Web](https://certifytheweb.com/), it will have
already created the necessary web.config file in the `/.well-known/acme-challenge` folder, so you do not need to do anything.

If your application does not create a web.config for you, or you are performing this process manually, create a `web.config` file
in the `/.well-known/acme-challenge` folder with the following contents:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
	<system.web>
		<authorization>
			<allow users="*" />
		</authorization>
	</system.web>

	<system.webServer>
		<validation validateIntegratedModeConfiguration="false" />
		
		<staticContent>
			<mimeMap fileExtension="." mimeType="text/json" />
		</staticContent>
		
		<handlers>
			<clear />
			<add name="StaticFile" path="*" verb="*" type="" modules="StaticFileModule,DefaultDocumentModule,DirectoryListingModule" scriptProcessor="" resourceType="Either" requireAccess="Read" allowPathInfo="false" preCondition="" responseBufferLimit="4194304" />
		</handlers>
	</system.webServer>
</configuration>
```

That's it! Your CMS should be set up to serve raw, unfiltered extensionless files from the `/.well-knwon/acme-challenge` folder.
