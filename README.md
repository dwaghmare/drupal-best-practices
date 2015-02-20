
## Drupal Website Launch Checklist ##
- Perform load testing & optimization beforehand include slow-log analyses;
- [Percona Toolkit](http://www.percona.com/doc/percona-toolkit/2.2/pt-query-digest.html) for MySQL can help to analyze slow logs;
- Check Drupal SEO Tools report;
- Match recommendations of the following tools:
	- [Security Review Module](https://drupal.org/project/security_review);
  - [Site Audit module](https://drupal.org/project/site_audit);
  - Acquia Insight;
  - YSlow;
- Install and configure [Security Kit module](https://www.drupal.org/project/seckit).
- Install [Username Enumeration Prevention module](https://www.drupal.org/project/username_enumeration_prevention). Also it's suggested to apply patches to it.
- Install and configure [Secure Pages](https://www.drupal.org/project/securepages) module.
- Run Performance and Scalability Checklist module;
- Analyze Coder Review results;
- Perform load testing using tools with cleared database cache, memcache and image styles when caches are disabled;
- Make sure you always have a failover scenario and a quick backup;
- Check your Maintenance message is appropriate (admin/config/development/maintenance)
- Configure Cron jobs;
- Disable all development modules(Devel, Views UI, Field UI etc). For quick enable/disable of dev modules and you have admin menu module installed, then configure it here /admin/config/administration/admin_menu under the "Performance" tab;
- Add console.log stub;
- Configure caches and reverse-proxies properly: APC, Varnish, Memcache, Akamai;
- Make sure that AJAX requests are cached properly;
- Enable bandwidth optimization;
    - Compress cached pages;
    - Aggregate and compress JS/CSS files;
    - Use [Advanced CSS/JS Aggregation](https://drupal.org/project/advagg) module instead of core aggregation;
- Test simultaneous an consequent anonymous access scenarios and behavior when every cache is enabled;
- Replace database logging functionality with other solutions, e.g. syslog;
- Disable any errors output on frontend;
- Enable fast_404 in settings.php file or use [Fast 404](https://drupal.org/project/fast_404) module;
- [Make sure file permissions for file directories and code directory are set correctly](http://drupal.org/node/244924);
- Make sure that input formats are correctly configured;
- On /admin/config/system/site-information make sure the email address and name are correct;
- Ensure website permissions are set appropriately and minimally;
- If using SSL, change your local /etc/hosts to point the site to its live domain and ensure SSL redirection is working correctly;
- Remove test content, such as "lorum ipsum" text, dummy users, or content generated by the Devel module;
- Check the maximum file upload sizes and maximum execution time;
- If you don't use core Search module, make sure it's disabled;
- If you use any replacements for standard Cron, make sure it's disabled;
- Warm caches before launch:
    - Use [Cache Warmer module](https://drupal.org/project/cache_warmer);
    - Use [HTTPRL Spider module](https://drupal.org/project/httprl_spider);
- If possible, perform launch component-by-component and one-by-one;
- Make sure your admin account has a strong password and it's changed before launch;
- Enable caching for Views output, Panels panes output and blocks. Useful modules are the following:
    - [Views Content Cache](https://drupal.org/project/views_content_cache).

## Gotchas ##
- If you maintain a very large number of files on your website, it can have a substantial negative effect on performance and stability, especially if they are all contained in the same directory.  If your site requires a large number of files, maintain them in multiple directories;
- Using standard Views pager might cause performance issues because of additional COUNT query. Use [Views Litepager](https://drupal.org/project/views_litepager) instead.
- Some conditions prevent the use of an in-memory temporary table, in which case the server uses an on-disk table instead:
	- Presence of a BLOB or TEXT column in the table;
	- Presence of any string column in a GROUP BY or DISTINCT clause larger than 512 bytes;
	- Presence of any string column with a maximum length larger than 512 (bytes for binary strings, characters for nonbinary strings) in the SELECTlist, if UNION or UNION ALL is used.


## Hiding traits of Drupal ##
To secure your a website from automated attacks it always better to hide the CMS:
- By removing CHANGELOG.txt file;
- By removing GENERATOR meta-tag (e.g. using https://www.drupal.org/project/remove_generator module);
- By hiding PHP fatal errors  (e.g. using https://www.drupal.org/project/hide_php_fatal_error module);
- By renaming admin paths (e.g. using https://www.drupal.org/project/rename_admin_paths module);
- By modifying the template of robots.txt file;
- By disallowing viewing of txt files in the core folder;
- By modifying Drupal default Expires header;
- By renaming default Drupal strings (e.g. using String Overrides module);
- By renaming default classes in Drupal theme;
- By changing default upload paths;
- By creation of custom 403/404 pages; (e.g. https://www.drupal.org/project/search404);
- By removing, renaming and/or updating update.php file;
- By removing, renaming and/or updating cron.php file;
- By updating default images, e.g. throbber.

## Handling [PSA-2014-003](https://www.drupal.org/PSA-2014-003) (Drupalgeddon) ##
 1. Immediately upgrade to Drupal 7.32, of it’s not possible at least patch the vulnerable function in /includes/database/database.inc file;
 2. Check files integrity using Git status or if not possible using Hacked;
 3. If possible, redeploy the latest version of the code base;
 4. Scan public / private files locations for *.php, *.sh and any other suspicious files. There are some strategies to improve this procedure: for instance, if only images uploads are allowed, then we scan scan for any files other than images;
 5. If the webserver setup is permissive in terms of permissions and users (e.g. Apache user can write anywhere), it will be additionally required to perform audit of the entire server;
 6. Install, Security review, Drupalgeddon, Site Audit contributed modules and execute these modules checks;
 7. If the pages are built using Features module and if allowed, then revert all the features to their original state;
 8. Rebuild the menu executing core ‘menu_rebuild()’ function;
 9. Review the `variable` table to find any suspicious values. On some installations it might be possible to truncate the table entirely, if Features module is used and if default values of variables are acceptable.
 10. Check the database for new MySQL users, update MySQL passwords and regenerate passwords/tokens for any systems, which are integrated;
 11. Check users’ roles to find if there is a user which have ‘admin’ role;
 12. Check `menu_router` and `users` tables for suspicious entries, e.g. with ‘file_put_contents’ callbacks;
 13. Dump the entire website HTML, e.g. using some crawler, and grep for additional parameters in links;
 14. If possible analyze sessions table for logins of admin/advanced users from external IP addresses and check their last login dates.
 15. Analyze Apache Logs for POST keys with strings 'UPDATE', 'INSERT', 'DELETE', ‘?q=node&destination=node’;
 16. Check places in Panels, Blocks, Views where custom PHP snippets might be used, e.g. in custom access rules.
 17. Set another Drupal hash salt in settings.file for passwords generation, reset passwords of all the users and send them new;
 18. Check the database for new MySQL users, update MySQL passwords and regenerate passwords/tokens for any systems, which are integrated;
 19. Set another Drupal hash salt in settings.file for passwords generation, reset passwords of all the users and send them new.
 20. PSA-2014-003 attacked your server files to send out spam emails for example, so if possible re-create your server files