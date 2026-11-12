# Mikrozone Tiki profile

**Target:** Tiki Wiki CMS Groupware 30.1  
**Profile ID/name:** Mikrozone (repository: `PetoJanis/tiki_Profile_mz`)  
**Status:** initial test profile – apply only to a clean test installation

This profile extends the official Tiki **Collaborative_Community_21** profile. It deliberately uses Tiki's built-in content types instead of introducing extra CMS extensions.

## Files

- `profile.yaml` – profile definition to apply in Tiki.
- `README.md` – feature design, content mapping and test notes.

## Applying the profile

1. Make a database and files backup of the test Tiki site.
2. Confirm that the test site is Tiki 30.1 and can reach `profiles.tiki.org`.
3. In Tiki Admin → Profiles, apply the official `Collaborative_Community_21` profile first if it is not already installed.
4. Apply this repository's `profile.yaml` as a local profile (or expose it through a Tiki custom profile repository). A GitHub repository URL itself is not a Tiki profile-repository endpoint; use the raw YAML/local-profile mechanism unless a Tiki repository endpoint is configured.
5. Inspect the created trackers, fields, categories and permissions before adding test data.
6. Do not import production content yet. This is still Stage 3 – functionality verification.

Profiles modify the database and permissions. Tiki does not provide a universal one-click rollback for all profile changes. Test on a disposable installation or restore the backup if the result is not acceptable.

## Functional design and e107 content mapping

| Mikrozone requirement / legacy e107 content | Tiki content type / feature | Proposed use |
|---|---|---|
| Novinky, announcements | **Articles** | Each legacy e107 news item becomes one Tiki Article. Preserve title, body, author, publication date, status and original e107 ID in migration metadata. Article categories distinguish news types. |
| Fórum – kategórie a diskusné oblasti | **Forums** | Tiki forums and forum categories. Legacy e107 forum sections map to Tiki forums/categories; topics and replies map to forum topics/posts. Preserve authors, dates, ordering, attachments and source IDs. |
| Lexikón | **Tracker: Lexikón Mikrozone** | One tracker item per term. Fields: Heslo, Vysvetlenie, Súvisiace názvy a skratky, Zdroj/odkaz. The term is searchable, public, listed and links to its item. The definition is public. Editors/moderators should own creation and approval during the first test. |
| HotWords | **Hotwords feature** | Native Tiki Hotwords, not a tracker. Migrate e107 hotword terms and target links into Tiki's Hotwords administration. The profile enables the feature; the actual term/link list is data and must be imported separately. |
| Technické návody, články a referenčný obsah | **Wiki pages / Wiki structures** | Use Wiki pages for collaboratively maintained, structured technical documentation. Existing e107 articles/news remain Articles; do not move all content into Wiki. |
| Kódové ukážky v obsahu | **Wiki syntax + syntax highlighter / Code plugin** | Enable Tiki syntax highlighting. Test CODE blocks with the languages actually used on Mikrozone before importing content. |
| Obrázky, súbory a prílohy | **File Galleries** | Central file storage for migrated e107 public files. Forum attachments remain associated with their forum posts where possible; other files go to suitable galleries. Preserve original file names and source IDs in migration metadata. |
| Používateľské skupiny | **Tiki Groups and permissions** | Use Anonymous, Registered, Editors, **Moderators** and Admins. Moderators inherit editor permissions and receive user-management rights only if explicitly assigned; they are not site administrators. Map the legacy e107 classes to these groups after reviewing all 10 source classes. |
| Verejný a rozšírený profil používateľa | **Tracker: Profilové údaje Mikrozone + Tiki user profile** | Preserve the full `e107_user_extended` dataset, not only the specialization. Known legacy fields include country, homepage, specialization and LinkedIn URL; inspect the actual source field definitions and values before defining the complete target field set and visibility. The specialization remains mandatory, public and deliberately not searchable. One tracker item per user associates data with the Tiki account. |
| Pôvodné štatistiky a história e107 | **Hidden migration fields in the same user tracker + native Tiki user fields where appropriate** | Keep original e107 ID, registration date, last visit, visits, posts and comments as legacy values. These are not marked public. Do not overwrite Tiki's own login/activity timestamps with historical values. Validate post/comment totals against imported content before displaying any totals. |
| Súkromné správy | **Tiki Messages** | Built-in inter-user messaging. Test send, receive, unread state, permissions and email notification behavior. |
| Sledovanie noviniek a diskusií | **Tiki Watches** | Built-in user/group watches and notification preferences. Test forum and article subscriptions. |
| E-mailové notifikácie | **Tiki mail / notification settings** | Profile enables the relevant feature areas, but SMTP/from-address/cron delivery are site-specific and must be configured and tested on the hosting environment. |
| Chat | **Existing Mikrozone AJAX Chat Tiki package** | Not replaced by Tiki MiniChat. The bridge already developed in Stage 2 remains a separate integration. The profile intentionally disables Tiki MiniChat to avoid two competing chat systems. |
| Blogy / používateľské denníky | **Tiki Blogs** | Enable native blogs. Migrate the 77 `e107_userjournals` entries with their authors, dates, comments and links. Do not treat news Articles as a substitute for blogs. |
| Katalóg firiem a služieb | **Tracker-based company/service directory** | Migrate 166 `e107_scontent_items`, 9 categories and 150 item relationships. Preserve company/service categories and predecessor/successor or other relationships. The exact Tracker fields must be designed after inspecting the legacy `f1`–`f9` definitions; do not guess their meaning. |
| Adresár webových odkazov | **Tiki Directory** | A separate feature from the company/service catalogue. Migrate 108 `e107_links` records into categorized Directory entries, retaining titles, URLs, descriptions and source IDs where available. |
| FAQ | **Wiki FAQ structure or FAQ Tracker** | Preserve 19 `e107_faq` entries and 11 related `e107_faq_info` records. Tiki's legacy FAQ feature is deprecated in current documentation, so use a maintainable Wiki/Tracker-based FAQ unless Tiki 30.1 testing proves the native FAQ feature is appropriate. |
| Kalendár | **Tiki Calendar** | Enable calendars and migrate 176 `e107_event` records with their 5 categories, dates, authors and descriptions. |
| Ankety | **Tiki Polls** | Enable native polls. There are 25 `e107_polls` records; inspect schema and decide how much of poll definitions and historical votes can be retained. |
| Newsletter | **Tiki Newsletters** | Enable the native newsletter feature and preserve the 293 archived newsletter entries where practical. Do not blindly import the 55,834 mailing-list rows; first establish what they represent and whether they are needed. |
| Downloads | **Tiki File Galleries** | Migrate 85 download entries and 29 categories, preserving descriptions, download links/files and source IDs. |
| Hodnotenia, reputácia a odomykanie funkcií | **Tiki ratings / Score + group/category permissions** | Preserve the intended read/add/edit/rate/comment and approval rules for public and private sections. Enable Score, but do not assume the old e107 score model or unlock rules transfer automatically; verify whether the required thresholds can be implemented natively without custom core changes. |
| Bug tracker | **Tiki Tracker or archived Wiki section** | Preserve the 14 legacy bug records and their category/status/priority/resolution metadata if they are still useful. Decide whether they should become active tracker items or a read-only archive. |
| Editor a technické formátovanie | **Tiki WYSIWYG, Wiki syntax, tables, Code plugin and syntax highlighter** | Test WYSIWYG editing, tables, code blocks, syntax highlighting, image galleries and file attachments before importing content. |
| Projekty KiCad / FreeCAD | **Wiki/Trackers with external GitHub links** | Provide project pages or structured project records linking to the relevant GitHub repositories, including KiCad libraries and FreeCAD projects. Preserve repository URLs and project descriptions; do not duplicate GitHub source files into Tiki without a reason. |
| Verejné a súkromné sekcie | **Tiki Groups, category permissions and object permissions** | Model access separately for reading, submitting, editing, rating, commenting and approval. Test each permission with Anonymous, Registered, Editors, Moderators and Admins accounts. |

## Tracker configuration details

### 1. Lexikón Mikrozone

The profile creates the following fields:

| Field | Tiki field type | Public | Searchable | Listed | Required |
|---|---|---:|---:|---:|---:|
| Heslo | Text field | yes | yes | yes | yes |
| Vysvetlenie | Text area | yes | no | no | yes |
| Súvisiace názvy a skratky | Text field | yes | yes | yes | no |
| Zdroj / odkaz | URL | yes | no | no | no |

The tracker supports comments. It is intended to replace the legacy e107 Lexikon content, not the Articles feature. Before migration, verify how the old plugin stores each term, aliases, descriptions, categories and links.

### 2. Profilové údaje Mikrozone

The tracker is configured for one item per user and creator modification. Its fields are:

- **Používateľ** – Tiki User selector, used as the account link.
- **Moja špecializácia (procesor, firma, alebo iné)** – public text area, mandatory at registration, deliberately not searchable.
- **Country** – public country selector, mandatory at registration.
- **Homepage** – public URL.
- **LinkedIn** – public URL (`user_linkedin_url`).
- **Jazyk** – language value from `user_language`; currently stored as text pending validation of the preferred Tiki-native language preference field.
- **Profilová fotografia** – source is `user_image`; use Tiki's native user image/avatar mechanism if it is available and validated. It is not duplicated into a tracker field in this profile.
- **E-mail** – remains the Tiki account email and is not exposed as a public tracker field. Public email display is disabled by default; preserve `user_hideemail` semantics during import.
- **Ďalšie sociálne odkazy** – the source inventory must determine the exact named fields and URLs. Do not collapse multiple named social links into one unstructured field or invent networks.
- **Pôvodné e107 ID** – private migration key.
- **Pôvodný dátum registrácie** – private legacy value.
- **Pôvodný posledný prístup** – private legacy value.
- **Pôvodný počet návštev** – private legacy value.
- **Pôvodný počet príspevkov** – private legacy value.
- **Pôvodný počet komentárov** – private legacy value.

The profile declares both **specialization and Country as mandatory registration fields** for the Registered group. Country is mandatory from registration (confirmed 12 Nov 2026). The profile also enables Tiki's user-tracker integration and account approval (`validateUsers: y`); this follows the official User_Trackers pattern but must still be verified in the actual Tiki 30.1 registration flow. Test that both fields are shown, required, saved against the new user, and available to an administrator reviewing a pending registration. If the registration UI does not expose it correctly, do not accept that as complete: use the Tiki-supported user tracker registration integration and adjust the profile after the test.

**Important:** the legacy statistics are storage for migration fidelity, not automatically trustworthy public counters. A migration importer must preserve the source values, map e107 user IDs to Tiki user IDs, and compare post/comment totals with the content actually imported.

## Additional features enabled / required for the complete Mikrozone site

The profile enables native Tiki Blogs, Calendar, Directory, Newsletters, Polls and Score in addition to the initial core. Their content models and permissions still need dedicated Stage 3 tests. The overall portal must also support public/private areas, section-level ACLs, content approval, WYSIWYG editing, tables, image galleries, attachments and links to external KiCad/FreeCAD GitHub projects. The company/service catalogue is distinct from the general links Directory and will use a Tracker because the legacy `scontent` records have custom fields and relationships.

Tiki's current documentation marks the legacy FAQ feature as deprecated. The FAQ content must still be migrated; the preferred maintainable target is a Wiki FAQ structure or a dedicated FAQ Tracker, not dropping the content.

## Features intentionally not created by this profile

- No new chat implementation – use the Stage 2 PHPFreeChat bridge.
- Blogs are a required separate content area; e107 news maps to Articles and user journals map to Tiki Blogs.
- No replacement forum extension – use native Tiki Forums.
- No custom PHP or Tiki core modifications.
- No automatic SMTP credentials, filesystem paths or cron entries – these are environment-specific.
- No automatic import of e107 content – that belongs to the migration importer after Stage 3 passes.

## Stage 3 test checklist

- [ ] Profile applies on a clean Tiki 30.1 installation without YAML/profile errors.
- [ ] Collaborative Community pages, menu and baseline permissions still work.
- [ ] Anonymous visitors can read public articles, forums, lexicon entries and public files, but cannot post or edit.
- [ ] Registered users can sign in, use private messages and post/reply in forums.
- [ ] Registration displays and requires the specialization field; an administrator can review it before approval.
- [ ] A user can view and edit their own specialization; another user can see it in the public profile.
- [ ] Specialization is not included in tracker search results.
- [ ] Editors can moderate forum content and lexicon entries without gaining full site administration.
- [ ] Moderators group exists under the correct name, inherits Editor rights and is not accidentally granted full site administration.
- [ ] Blogs are enabled and legacy user journals can be represented with authors, dates and comments.
- [ ] Company/service catalogue and general links Directory are separate, categorized and retain their legacy relationships/URLs.
- [ ] FAQ entries and related information have a defined Wiki/Tracker target and remain searchable/readable.
- [ ] Calendar events and categories are enabled and can be migrated.
- [ ] Polls, newsletter archive, downloads and legacy ratings/Score have explicit migration decisions and working permissions.
- [ ] All meaningful `e107_user_extended` fields have been inventoried and mapped with correct privacy settings.
- [ ] Lexicon items can be created, listed, searched and opened; comments/links behave correctly.
- [ ] Articles can be published with author, date, categories and attachments.
- [ ] HotWords can be created and automatically link matching words in content.
- [ ] Wiki CODE blocks render and syntax highlighting works for representative languages.
- [ ] File galleries accept and serve files with the intended permissions.
- [ ] Tiki Watches and email notifications work end-to-end with the actual hosting mail configuration.
- [ ] Existing PHPFreeChat bridge still works; Tiki MiniChat remains disabled.
- [ ] No legacy e107 statistic is shown as a new Tiki statistic.

## Known limitations / items to validate

1. This is an initial profile draft, not yet validated by applying it to the user's Tiki 30.1 database. The profile tester and the actual site installation are the acceptance tests.
2. Tiki profile handlers and preference names can change between major versions. The profile is targeted at Tiki 30.1; do not assume compatibility with another major release.
3. The user tracker registration-field binding is based on Tiki's User_Trackers profile pattern. The actual registration and approval workflow must be tested on Tiki 30.1.
4. The base Collaborative Community profile has its own default forum and pages. Review its English labels and rename or reorganize them for Mikrozone after applying the profile.
5. The profile does not create all final navigation/menu placements. First validate content objects and permissions; then finalize the menu against the site's chosen theme/layout.
6. Tiki profile application is not a transactional migration. Always back up before applying or reapplying it.

## Reference documentation

- [Collaborative Community 21 profile](https://profiles.tiki.org/Collaborative_Community_21)
- [Tiki Tracker Handler](https://profiles.tiki.org/Tracker+Handler)
- [User Trackers profile](https://profiles.tiki.org/User_Trackers)
- [Creating a Tracker](https://doc.tiki.org/Creating-a-Tracker)
- [Tracker Field Types](https://doc.tiki.org/Tracker-Field-Types)
- [Custom Profile Repository](https://profiles.tiki.org/Custom+Repository)


## Audit update – 12 November 2026

The profile has been revised after reviewing the Tiki public profiles and the Mikrozone migration inventory.

### User tracker and registration

- The user tracker now includes the known source profile data: specialization, Country, Homepage, LinkedIn and language, alongside the private e107 migration metadata.
- Country and specialization are both declared as mandatory registration fields.
- The configuration now enables Tiki's user-tracker integration preferences (`userTracker`, `trackerfield_userpreference` and `user_trackersync_trackers`) following the structure of the official [User_Trackers profile](https://profiles.tiki.org/User_Trackers).
- New account registration is enabled and account validation is set to administrator approval (`validateUsers: y`). The public example profile uses `validateUsers: n` for its own demonstration; that setting is intentionally not copied because Mikrozone requires approval.
- Email is not a public tracker field. The profile sets the global user-information preference to private; the actual profile display and email-hiding behavior must be checked with Anonymous and Registered test accounts.
- The source avatar field `user_image` is not represented as an ordinary text field. Validate Tiki's native user-image mechanism and preserve the original file mapping in the importer.
- Additional social links are deliberately not guessed. Complete their named fields after the full `e107_user_extended` field inventory is confirmed.

### Groups and inherited profile

- The target groups defined here are `Editors` (Redaktori), `Moderators`, `Neaktívny` and `Na odstránenie`, in addition to `Registered`, `Anonymous` and `Admins`.
- `Moderators` inherits `Editors`; it is not named `SubAdmins`.
- The official `Collaborative_Community_21` dependency may itself create a `SubAdmins` group and baseline permissions. This profile does not assume that dependency groups are automatically deleted. Inspect the resulting group list and permissions after applying it; do not assign Mikrozone users to `SubAdmins`.
- The `Neaktívny` and `Na odstránenie` groups are created as migration/administrative target groups. Their membership and final deny/limited-access rules are assigned by the importer or a separately reviewed permission step, not guessed here.

### Feature switches versus content objects

This profile enables the native feature areas required by the migration (Articles, Blogs, Calendar, Directory, Forums, File Galleries, Messages, Newsletters, Polls, Score, Trackers, Wiki and HotWords). Enabling a feature does **not** create the final Mikrozone content model, menu, categories, galleries, trackers, or all ACL rules.

The current profile explicitly creates only the initial categories, Lexikón tracker and user-profile tracker. The following still require separate design and Tiki 30.1 verification before content import:

- Articles and the two news types `Bleskovka` and `Novinka`.
- Company/service directory Tracker, including the legacy item relationships.
- FAQ structure (Wiki or Tracker).
- Blog permissions and private/public behavior.
- Calendar categories, recurring events and homepage teaser.
- Gallery hierarchy and per-user/private gallery permissions.
- Historical private-message import and message privacy.
- Final category/object permissions and navigation.

### Test status

These changes are a profile-definition update based on the audit, **not proof that the profile has been successfully applied to Tiki 30.1**. In particular, verify registration field synchronization, mandatory validation, approval workflow, public specialization/Country display, email privacy, user-image behavior and the resulting group inheritance on a disposable clean installation. Do not import production data until those tests pass.
