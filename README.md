# ai-news-platform-config
Configurations used by ai news platform config server

## Correct naming pattern for config files
Spring Cloud Config expects ONE of these correct patterns:

- Valid naming pattern 1:
  ```
  user_profile/application.yml
  user_profile/application-dev.yml

  ```
- Valid naming pattern 2:
  ```
  user_profile.yml
  user_profile-dev.yml
  ```

## Troubleshoot
You're seeing Eureka Server configs mixed into User Profile configs because your Config Server is loading everything in the root of the Git repo, and Spring Cloud Config merges all application.yml files unless properly isolated.

This happens when:

✅ Root-level application.yml or another app’s config matches your request pattern

Spring Cloud Config merges config files using this priority:

{application}-{profile}.yml

{application}.yml

application-{profile}.yml

application.yml

If you have this structure:

eureka_server/
    application.yml   <-- LOADED because the name "application" applies to ALL services!
user_profile/
    application.yml
application.yml       <-- GLOBAL config, applies to all apps


Then Config Server will always load any file named application.yml from any folder, unless your repo structure is isolated correctly.

✅ Why Eureka config is showing up
{
  "name": "https://github.com/.../eureka_server/application.yml"
}


This means the config server thinks:

eureka_server/application.yml is a global config because it matches the name application.yml.

Spring Cloud Config treats any folder containing application.yml as global unless:

It matches {application}.yml, or

It matches {application}/application.yml.

In your case:

eureka_server/application.yml   <-- interpreted as global!


This is why it appears even when requesting:

http://localhost:8071/user_profile/default

✅ Correct structure
eureka-server/
    eureka-server.yml
    eureka-server-default.yml   <-- optional profiles
user-profile/
    user-profile.yml
    user-profile-default.yml
