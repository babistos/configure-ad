<h3><strong>How to Configure On-Premises Active Directory within Azure VMs</strong></h3>
<p>Configuring <strong>On-Premises Active Directory (AD) within Azure Virtual Machines (VMs)</strong> allows you to extend your organization's directory services into the cloud. This setup enables user authentication, Group Policy management, and integration with other Azure services.</p>
<hr />
<h2><strong>🛠 Prerequisites</strong></h2>
<ol>
<li><strong>Azure Subscription</strong> &ndash; Ensure you have an active subscription.</li>
<li><strong>Azure Virtual Network (VNet)</strong> &ndash; Set up a virtual network with at least one subnet.</li>
<li><strong>Azure Virtual Machine (Windows Server)</strong> &ndash; A VM running <strong>Windows Server 2019 or 2022</strong>.</li>
<li><strong>Static Private IP Address</strong> &ndash; Assign a static internal IP to your AD server.</li>
<li><strong>Remote Desktop (RDP) Access</strong> &ndash; Ensure you can RDP into the VM.</li>
</ol>
<hr />
<h2><strong>Step 1: Deploy an Azure VM for Active Directory</strong></h2>
<ol>
<li><strong>Log in to Azure Portal</strong>: <a href="https://portal.azure.com/">Azure Portal</a>.</li>
<li><strong>Create a Virtual Machine</strong>:
<ul>
<li>Navigate to <strong>Azure Virtual Machines</strong>.</li>
<li>Click <strong>"Create" &rarr; "Azure Virtual Machine"</strong>.</li>
<li>Choose <strong>Windows Server 2019 or 2022</strong> as the image.</li>
<li>Select a VM size (e.g., <strong>Standard B2ms</strong> for small setups).</li>
<li>Set <strong>Administrator Username &amp; Password</strong>.</li>
<li>Under <strong>Networking</strong>, select an <strong>existing VNet</strong> or create a new one.</li>
<li>Allow <strong>RDP (3389) and DNS (53) ports</strong> in the security group.</li>
<li>Click <strong>Create</strong> and wait for deployment.</li>
</ul>
</li>
</ol>
<hr />
<h2><strong>Step 2: Assign a Static Internal IP to the AD Server</strong></h2>
<ol>
<li>Go to <strong>Azure Portal &rarr; Virtual Machines</strong>.</li>
<li>Select your <strong>VM &rarr; Networking</strong>.</li>
<li>Click on the <strong>attached network interface</strong>.</li>
<li>Under <strong>IP Configurations</strong>, select the primary IP and change <strong>Assignment</strong> to <strong>Static</strong>.</li>
<li>Set an <strong>IP address (e.g., 10.0.0.4)</strong>.</li>
<li>Save changes.</li>
</ol>
<hr />
<h2><strong>Step 3: Install Active Directory Domain Services (AD DS)</strong></h2>
<ol>
<li><strong>RDP into the VM</strong> using the public IP.</li>
<li>Open <strong>Server Manager</strong> and click <strong>"Add Roles and Features"</strong>.</li>
<li>Select <strong>"Role-based or feature-based installation"</strong> and click <strong>Next</strong>.</li>
<li>Choose your <strong>server</strong> and click <strong>Next</strong>.</li>
<li>Under <strong>Server Roles</strong>, select:
<ul>
<li><strong>Active Directory Domain Services</strong>.</li>
<li>Accept the required dependencies and click <strong>Next</strong>.</li>
</ul>
</li>
<li>Click <strong>Install</strong> and wait for the installation to finish.</li>
</ol>
<hr />
<h2><strong>Step 4: Promote the Server to a Domain Controller</strong></h2>
<ol>
<li>Open <strong>Server Manager &rarr; AD DS</strong>.</li>
<li>Click <strong>"Promote this server to a domain controller"</strong>.</li>
<li>Select <strong>"Add a new forest"</strong> and enter a <strong>Root Domain Name</strong> (e.g., <code>corp.local</code>).</li>
<li>Set a <strong>Directory Services Restore Mode (DSRM) password</strong>.</li>
<li>Configure <strong>DNS options</strong> (leave default).</li>
<li>Verify NetBIOS name and click <strong>Next</strong>.</li>
<li>Keep default paths for database, logs, and SYSVOL.</li>
<li>Click <strong>Install</strong> and <strong>restart</strong> the VM.</li>
</ol>
<hr />
<h2><strong>Step 5: Configure DNS &amp; Network Settings</strong></h2>
<ol>
<li>Go to <strong>Server Manager &rarr; Tools &rarr; DNS</strong>.</li>
<li>Expand your domain and verify that DNS records exist.</li>
<li>Set the <strong>Azure VNet to use the AD server as the primary DNS</strong>:
<ul>
<li>Go to <strong>Azure Portal &rarr; Virtual Network</strong>.</li>
<li>Select <strong>DNS Servers</strong>.</li>
<li>Change to <strong>Custom</strong> and enter your AD server&rsquo;s private IP (e.g., <code>10.0.0.4</code>).</li>
</ul>
</li>
</ol>
<hr />
<h2><strong>Step 6: Create User Accounts &amp; Groups</strong></h2>
<ol>
<li>Open <strong>Active Directory Users and Computers (ADUC)</strong> (<code>dsa.msc</code>).</li>
<li>Expand your domain (<code>corp.local</code>).</li>
<li>Right-click <strong>Users &rarr; New &rarr; User</strong>.</li>
<li>Create an account (e.g., <code>johndoe</code>).</li>
<li>Assign a password and group memberships.</li>
</ol>
<hr />
<h2><strong>Step 7: Join Additional Azure VMs to the Domain</strong></h2>
<ol>
<li>RDP into another Azure VM.</li>
<li>Open <strong>System Properties</strong> (<code>sysdm.cpl</code>).</li>
<li>Click <strong>Change Settings</strong> &rarr; <strong>Change</strong>.</li>
<li>Select <strong>Domain</strong> and enter the AD domain (e.g., <code>corp.local</code>).</li>
<li>Provide AD credentials.</li>
<li>Restart the VM.</li>
</ol>
<hr />
<h2><strong>Step 8: Configure Group Policies (GPO)</strong></h2>
<ol>
<li>Open <strong>Group Policy Management (<code>gpmc.msc</code>)</strong>.</li>
<li>Create a <strong>new policy</strong> (e.g., "Security Settings").</li>
<li>Apply rules like:
<ul>
<li>Password Policies.</li>
<li>Login Restrictions.</li>
<li>Software Deployment.</li>
</ul>
</li>
</ol>
<hr />
<h2><strong>Step 9: Secure the Active Directory</strong></h2>
<ol>
<li><strong>Enable Azure Backup</strong> to protect AD data.</li>
<li><strong>Limit RDP Access</strong> using <strong>Azure Network Security Groups (NSG)</strong>.</li>
<li><strong>Monitor AD Logs</strong> using <strong>Azure Monitor</strong>.</li>
</ol>
<hr />
<h3><strong>🎯 Summary</strong></h3>
<table>
<thead>
<tr>
<th><strong>Step</strong></th>
<th><strong>Action</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><strong>1</strong></td>
<td>Deploy an Azure VM with Windows Server</td>
</tr>
<tr>
<td><strong>2</strong></td>
<td>Assign a Static Private IP</td>
</tr>
<tr>
<td><strong>3</strong></td>
<td>Install Active Directory Domain Services (AD DS)</td>
</tr>
<tr>
<td><strong>4</strong></td>
<td>Promote VM to a Domain Controller</td>
</tr>
<tr>
<td><strong>5</strong></td>
<td>Configure DNS and Networking</td>
</tr>
<tr>
<td><strong>6</strong></td>
<td>Create Users and Groups</td>
</tr>
<tr>
<td><strong>7</strong></td>
<td>Join Additional VMs to AD</td>
</tr>
<tr>
<td><strong>8</strong></td>
<td>Configure Group Policies (GPO)</td>
</tr>
<tr>
<td><strong>9</strong></td>
<td>Secure the AD Environment</td>
</tr>
</tbody>
</table>
<hr />
<h3><strong>✅ Next Steps</strong></h3>
<ul>
<li>Integrate <strong>Azure AD Connect</strong> for hybrid identity management.</li>
<li>Enable <strong>Multi-Factor Authentication (MFA)</strong> for added security.</li>
<li>Automate user provisioning with <strong>PowerShell scripts</strong>.</li>
</ul>
<p>This guide ensures a fully functional on-premises Active Directory running in Azure! Let me know if you need additional configurations. 🚀</p>
<p>&nbsp;</p>
<!-- Comments are visible in the HTML source only -->
