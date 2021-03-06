<properties
	pageTitle="Azure Active Directory B2C preview: Create an Azure Active Directory B2C tenant | Microsoft Azure"
	description="A topic on how to create an Azure Active Directory B2C tenant"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/25/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C preview: Create an Azure AD B2C tenant

To start using Microsoft Azure Active Directory (Azure AD) B2C, follow the three steps outlined in this article.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Step 1: Sign up for an Azure subscription

If you already have an Azure subscription, skip this step. If not, sign up for an [Azure subscription](../active-directory/sign-up-organization.md) and get access to Azure AD B2C.

> [AZURE.NOTE]
Currently, you can use Azure AD B2C preview for free but its use is limited to 50,000 users per tenant. An Azure subscription is required to access the [Azure classic portal](http://manage.windowsazure.com/).

## Step 2: Create an Azure AD B2C tenant

Use the following steps to create a new Azure AD B2C tenant. Currently B2C features can't be turned on in your existing directories, if you have any.

1. Sign in to the [Azure classic portal](https://manage.windowsazure.com/) as the Subscription Administrator. This is the same work or school account or the same Microsoft account that you used to sign up for Azure.
2. Click **New** > **App Services** > **Active Directory** > **Directory** > **Custom Create**.

    ![Screen shot of starting to create a tenant](./media/active-directory-b2c-get-started/new-directory.png)

3. Choose the **Name**, **Domain Name** and **Country or Region** for your tenant.
4. Check the option that says **This is a B2C directory**.
5. Click the check mark to complete the action.

    ![Screen shot of the check mark to create a B2C directory](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. Your tenant is now created and will appear in the Active Directory extension. You are also made a Global Administrator of the tenant. You can add other Global Administrators as required.

    > [AZURE.IMPORTANT]
    It can take up to two minutes for your tenant to be created. If you face issues during tenant creation, see [Creating an Azure AD Tenant or an Azure AD B2C Tenant - Issues & Resolutions](active-directory-b2c-support-create-directory.md) for guidance.

## Step 3: Navigate to the B2C features blade on the Azure portal

1. Navigate to the Active Directory extension on the navigation bar on the left side.
2. Find your tenant under the **Directory** tab and click it.
3. Click the **Configure** tab.
4. Click the **Manage B2C settings** link in the **B2C administration** section.

    ![Screen shot of directory configuration for B2C](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

5. The Azure portal with the B2C features blade showing will open in a new browser tab or window.

    > [AZURE.IMPORTANT]
    There is a known issue where this page doesn't load properly (for a small number of tenants). Refreshing the browser should fix it. If not, please contact Azure Support.

6. Pin this blade to your Startboard for easy access. (The Pin tool is marked in red at the upper-right corner of the features blade.)

    ![Screen shot of the B2C features blade](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]
    You can manage users and groups, self-service password reset configuration, and company branding features of your tenant on the [Azure classic portal](https://manage.windowsazure.com/).

## Next steps

Learn how to register an application with Azure AD B2C and to build a Quick Start application by reading [Azure Active Directory B2C preview: Register your application](active-directory-b2c-app-registration.md).
