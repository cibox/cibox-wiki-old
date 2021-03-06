hook_update_N()
=====

For approaching update path for adopted site, best practice is to have master module for following Drupal update path. Our best practice is to have prj_master module with hook_update_Ns only.
You need to enable the module for ability to have updates executed on your local dev or on builds.
We have preconfigured configs where you can just put the module name and its state for enabling it.

from global_env.yml
```yml
---
global_env:
  pre_settings:
    - { name: '$conf["pp_environment"]', status: true, value: "{{ pp_environment }}" }
    - { name: '$conf["file_temporary_path"]', status: true, value: '{{ tmp_folder }}' }
  modules: 
    - { name: 'prj_master', status: true }
  drush_commands: []
  post_settings:
    - { name: '$conf["pp_environment"]', status: true, value: "{{ pp_environment }}" }
    - { name: '$conf["cache_default_class"]', status: false, value: 'MemCacheDrupal' }
```
But, you know, enabling module is not enough in terms of update path.
There is a trick you need to follow:

```php
/**
 * Implements hook_install().
 */
function prj_master_install() {
  for ($i = 7000; $i < 7999; $i++) {
    $function = 'prj_master_update_' . $i;
    if (function_exists($function)) {
      $function();
    }
  }
}
```
This will give you an ability to control which numners of hook_update_N should be executed untill you will get latest database from production/staging with already enabled module. Then your hook_install never be executed, because module already enabled within database and rest of hook_update_Ns processed by regular ```drush updatedb``` step