---
title: Zewnętrzni dostawcy uwierzytelniania OAuth
author: rick-anderson
description: Odnajdywanie zewnętrznych dostawców uwierzytelniania OAuth, które współpracują z ASP.NET Core aplikacjami.
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
uid: security/authentication/otherlogins
ms.openlocfilehash: 2bc9a11d0a46e54b4206f846d187b8c1cc954f89
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78666265"
---
# <a name="external-oauth-authentication-providers"></a>Zewnętrzni dostawcy uwierzytelniania OAuth

[Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Rastogi](https://github.com/rustd)i [Valeriy Novytskyy](https://github.com/01binary)

Poniższa lista zawiera typowe dostawcy uwierzytelniania OAuth zewnętrznych, którzy pracują z aplikacjami ASP.NET Core. Pakiety NuGet innych firm, takie jak te obsługiwane przez [ASPNET-contrib](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth), mogą służyć do uzupełnienia dostawców uwierzytelniania wdrożonych przez zespół ASP.NET Core.

* [LinkedIn](https://www.linkedin.com/developer/apps) ([instrukcje](https://developer.linkedin.com/docs/oauth2))

* [Usługi Instagram](https://www.instagram.com/developer/register/) ([instrukcje](https://www.instagram.com/developer/authentication/))

* [Reddit](https://www.reddit.com/login?dest=https%3A%2F%2F www.reddit.com%2Fprefs%2Fapps) ([instrukcje](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example))

* [GitHub](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Fsettings%2Fapplications%2Fnew) ([instrukcje](https://developer.github.com/v3/oauth/))

* [Yahoo](https://login.yahoo.com/config/login?src=devnet&.done=http%3A%2F%2Fdeveloper.yahoo.com%2Fapps%2Fcreate%2F) ([instrukcje](https://developer.yahoo.com/bbauth/user.html))

* [Tumblr](https://www.tumblr.com/oauth/apps) ([instrukcje](https://www.tumblr.com/docs/api/v2#auth))

* [Pinterest](https://www.pinterest.com/login/?next=http%3A%2F%2Fdevsite%2Fapps%2F) ([instrukcje](https://developers.pinterest.com/docs/api/overview/?))

* [Pocket](https://getpocket.com/developer/apps/new) ([instrukcje](https://getpocket.com/developer/docs/authentication))

* [Flickr](https://www.flickr.com/services/apps/create) ([instrukcje](https://www.flickr.com/services/api/auth.oauth.html))

* [Dribble](https://dribbble.com/signup) ([instrukcje](https://developer.dribbble.com/v1/oauth/))

* [Vimeo](https://vimeo.com/join) ([instrukcje](https://developer.vimeo.com/api/authentication))

* [SoundCloud](https://soundcloud.com/you/apps/new) ([instrukcje](https://developers.soundcloud.com/blog/we-love-oauth-2))

* [VK](https://vk.com/apps?act=manage) ([instrukcje](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites))

[!INCLUDE[Multiple authentication providers](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]
