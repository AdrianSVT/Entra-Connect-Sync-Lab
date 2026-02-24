# Hybrid Identity Lab — Syncing On-Premises Active Directory to Microsoft Entra ID Using Entra Connect Sync

## Overview

In this lab, I extended my on-premises Active Directory environment to the cloud by integrating it with Microsoft Entra ID (formerly Azure Active Directory) using Entra Connect Sync. The goal was to synchronize user accounts and groups from my local domain controller to the cloud, creating a hybrid identity environment. This mirrors how many modern organizations manage their infrastructure, maintaining an on-premises Active Directory while leveraging the cloud for scalability and remote access. Hybrid identity environments are extremely common in enterprise settings, making this a critical skill in cloud and identity administration.

---

## Step 1: Preparing the On-Premises Environment

The first step was to create a dedicated Organizational Unit in Active Directory that would serve as the container for everything I wanted to sync to the cloud. Rather than synchronizing the entire directory, creating a specific OU gives me full control over exactly which users and groups get pushed to Entra ID, which is a much cleaner and more deliberate approach.

I navigated to **Active Directory Users and Computers** from the Server Manager dashboard by clicking **Tools → Active Directory Users and Computers.** Once the window opened, I right-clicked `mydomain.com`, selected **New → Organizational Unit**, and named it `Entra Connect Sync`.

<img width="227" height="200" alt="creating Entra Connect OU" src="https://github.com/user-attachments/assets/a1f85a18-715a-404c-ab3b-a6baad6db729" />

With the OU created, I populated it with a few users and groups that I wanted to sync. Refer to [This Lab] for the steps on creating new users and groups in Active Directory.

<img width="283" height="238" alt="creation of user, groups" src="https://github.com/user-attachments/assets/85bfcaf4-9421-4d74-a52e-d3e46ccf824e" />

Before moving on, there was one more thing to take care of on the server. I navigated back to the Server Manager dashboard, clicked **Local Server** in the left-hand menu, and located the **IE Enhanced Security Configuration** setting. I turned this off, as leaving it enabled can interfere with the Entra Connect Sync installation and setup process.

<img width="224" height="236" alt="turning of IE secuirty " src="https://github.com/user-attachments/assets/d48e67f2-6b2a-4dbc-8d5f-042d58377980" />


---

## Step 2: Downloading and Installing Entra Connect Sync

With the on-premises environment prepared, I moved over to the Azure portal to download the Entra Connect Sync agent. Using the search bar, I searched for **Microsoft Entra ID** and navigated to its page. 

<img width="290" height="136" alt="searching for entra id" src="https://github.com/user-attachments/assets/92a36bfc-43bf-49d4-8c05-a7cad70d1784" />

On the left-hand side, I expanded **Manage** and scrolled down to **Microsoft Entra Connect.** 

<img width="137" height="467" alt="scrolling down to Entra connect" src="https://github.com/user-attachments/assets/c2ed2f6f-a7d4-4116-b8ce-15b5c3a5f989" />

From there, I clicked the **Get Started** tab, then **Manage**, and scrolled down to find the download options. I selected the **Download Connect Sync Agent**, accepted the terms, and downloaded it.

<img width="782" height="735" alt="Entra connect page and accept terms download" src="https://github.com/user-attachments/assets/91ac0ce7-dcab-40d3-9b9e-655a81a113f9" />

Once the download was complete, I opened the file, which launched the **Microsoft Entra Connect Sync** setup wizard.

<img width="445" height="316" alt="first appearance of wizard" src="https://github.com/user-attachments/assets/48c6659a-d12d-4855-9c48-950078216b7a" />


---

## Step 3: Configuring the Wizard

I agreed to the license terms and clicked **Continue.** On the **Express Settings** page, rather than accepting the defaults, I clicked **Customize** to have full control over the configuration.

<img width="451" height="320" alt="express settiings page" src="https://github.com/user-attachments/assets/f7fcb338-7762-4e45-b100-909f3cfdc702" />

In the **Install Required Components** window, I left all optional boxes unchecked and clicked **Install** to proceed with a clean base installation.

<img width="448" height="318" alt="install required components" src="https://github.com/user-attachments/assets/d1b8a908-c0fe-459a-88e7-866bfe130122" />

Once the installation completed, I was brought to the **User Sign-In** page, which is one of the most important steps in the wizard. This is where I selected the authentication method that would be used to synchronize identities between my on-premises directory and Entra ID. Out of the five available options, I selected **Password Hash Synchronization.** This method takes the hashed passwords from my on-premises Active Directory and synchronizes them to Microsoft Entra ID, allowing users to sign in to the cloud using the same credentials they use on-premises. I clicked **Next.**

<img width="446" height="318" alt="user sign-on" src="https://github.com/user-attachments/assets/97702226-409c-45d9-8626-2529f0f096fa" />

On the following page, I was prompted to enter my Microsoft Entra ID Hybrid Identity Administrator or Global Administrator credentials. 

<img width="452" height="325" alt="enter my miicrossoft entra domain" src="https://github.com/user-attachments/assets/f662c3a2-e763-44f1-a870-38f970ce9a40" />

After entering my username and clicking **Next**, a separate authentication window appeared asking me to verify my Microsoft account. Once authenticated, I was taken to the next step.

<img width="287" height="256" alt="sign into microsoft" src="https://github.com/user-attachments/assets/fad5c8a3-2aac-4f62-8866-2b6587eb7ddc" />

---

## Step 4: Connecting the On-Premises Directory

This page is where I established the connection between my on-premises Active Directory and Entra ID. I set the **Directory Type** to **Active Directory** and selected `mydomain.com` as the forest. I then clicked **Add Directory.**

<img width="449" height="319" alt="connecting directories" src="https://github.com/user-attachments/assets/1b42b98b-b314-4449-bbad-5e6836d3b69b" />

A prompt appeared explaining that an AD account with sufficient permissions is required to facilitate periodic synchronization between the on-premises directory and the cloud. The wizard offered two options: **Create a new AD account** or **Use an existing AD account.** I chose to create a new account, populated the **Enterprise Admin Username** field with my domain credentials, set a password, and clicked **OK → Next.**

<img width="308" height="247" alt="create AD account for me" src="https://github.com/user-attachments/assets/132c2681-9fa2-4daf-b609-ca980df7e081" />

---

## Step 5: Domain Verification and UPN Configuration

The next section of the wizard checks whether you own a verified public domain. Since this is a lab environment, I do not have a verified custom domain, so I checked the option that reads **"Continue without matching all UPN suffixes to verified domains."** This allows the sync to proceed using the Entra ID tenant domain instead.
This page also asked me to select the on-premises attribute that would be used as the Microsoft Entra ID username. Under **USER PRINCIPAL NAME**, I selected `userPrincipalName` from the dropdown. This means users will be identified in the cloud using an email address-style username that matches their on-premises login. I clicked **Next** to continue.

<img width="442" height="310" alt="check domain verifiied or not" src="https://github.com/user-attachments/assets/9dc8e724-afef-4b2a-a92c-b6a25586bca7" />


---

## Step 6: Selecting OUs to Sync

By default, Entra Connect Sync wants to synchronize every object in Active Directory. Since I only wanted to sync a specific set of users and groups, I selected **Sync selected domains and OUs** instead. I unchecked `mydomain.com` at the top level, expanded it, and then checked only the `Entra Connect Sync` OU that I had created earlier. This ensures that only the users and groups inside that OU will be synchronized to the cloud. I clicked **Next** when finished.

<img width="449" height="322" alt="selection of OUs to sync" src="https://github.com/user-attachments/assets/5f397065-efe7-441c-a03b-ac074f3670ae" />

---

## Step 7: Uniquely Identifying Users and Optional Features

On the **Uniquely identifying your users** page, I selected **"Users are represented only once across all directories"** to define how on-premises users are identified, and **"Let Azure manage the source anchor"** for how they are identified in Entra ID. I clicked **Next** to continue.

<img width="446" height="315" alt="uniquely identifying users" src="https://github.com/user-attachments/assets/b84497a9-2a60-4e62-b2e0-fae12c889d98" />

On the **Filter Users and Devices** page, I kept the default setting of **Synchronize all users and devices**, which in this context only applies to the OU I selected in the previous step.

<img width="449" height="319" alt="filter users and devices" src="https://github.com/user-attachments/assets/d8d4ce92-47ff-4b88-af76-ddf78d7cb10e" />

On the **Optional Features** page, **Password Hash Synchronization** was already enabled. I also enabled **Password Writeback**, which means any password changes made in Microsoft Entra ID will automatically be written back to the on-premises Active Directory, keeping both environments in sync. I clicked **Next** to proceed.

---

## Step 8: Final Configuration and Installation

I had now reached the **Ready to Configure** screen. The option **"Start the synchronization process when configuration completes"** was automatically enabled, and I kept it selected to kick off the first sync immediately after installation. I clicked **Install** and waited for the configuration to complete.

<img width="451" height="320" alt="ready to configure" src="https://github.com/user-attachments/assets/a53c623a-7a02-4cb1-b2d4-42f5f214a3bd" />

---

## Verification

With the installation finished, it was time to confirm that everything had synced correctly. I navigated back to the Azure portal, searched for **Microsoft Entra ID**, and went to **Manage → Users** on the left-hand side. As shown below, the users from my on-premises Active Directory had successfully synced to Entra ID, confirming that the hybrid identity environment was fully operational.

<img width="499" height="113" alt="users on entra " src="https://github.com/user-attachments/assets/7bf069ae-6949-480f-893f-80c6049fcdae" />


