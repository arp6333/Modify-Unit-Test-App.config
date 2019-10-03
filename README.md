# Modifying a unit test app.config file

In the past I have tried to include an app.config file but there are times where the file is ignored and I am not sure why. Here is my temporary work around:

1. Insert the following method, [thank you to stackoverflow](https://stackoverflow.com/questions/4337201/net-nunit-test-assembly-getentryassembly-is-null), into your unit test class:

  ```csharp
  /// <summary>
  /// Allows setting the Entry Assembly when needed. 
  /// Use AssemblyUtilities.SetEntryAssembly() as first line in XNA ad hoc tests.
  /// </summary>
  /// <param name="assembly">Assembly to set as entry assembly</param>
  public static void SetEntryAssembly(Assembly assembly)
  {
      AppDomainManager manager = new AppDomainManager();
      FieldInfo entryAssemblyfield = manager.GetType().GetField("m_entryAssembly", BindingFlags.Instance | BindingFlags.NonPublic);
      entryAssemblyfield.SetValue(manager, assembly);

      AppDomain domain = AppDomain.CurrentDomain;
      FieldInfo domainManagerField = domain.GetType().GetField("_domainManager", BindingFlags.Instance | BindingFlags.NonPublic);
      domainManagerField.SetValue(domain, manager);
  }
  ```
  
2. Call the method from your TestInitialize class:

  ```csharp
  SetEntryAssembly(Assembly.GetCallingAssembly());
  ```

3. Print 'AppDomain.CurrentDomain.SetupInformation.ConfigurationFile' to see what file your unit test is using as the config file. For example, the path to mine was 'C:\PROGRAM FILES (X86)\MICROSOFT VISUAL STUDIO 14.0\COMMON7\IDE\COMMONEXTENSIONS\MICROSOFT\TESTWINDOW\vstest.executionengine.x86.exe.Config' and yours should be something similar. You will need admin rights to modify this file.

4. Copy and paste the contents of the config file you want to use into the contents of that config file. If you have multiple app.config files for different projects/solutions then you will need to combine them into this massive file. Yeah I know it sucks but it works for a temporary work-around.
