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
| Používateľské skupiny | **Tiki Groups and permissions** | Use the built-in Anonymous, Registered, Editors, SubAdmins and Admins hierarchy as the starting point. Mikrozone-specific roles (e.g. moderators and business users) need final naming and membership mapping during the migration design. |
| Verejný profil – špecializácia | **Tracker: Profilové údaje Mikrozone** | One tracker item per user. The public field is exactly “Moja špecializácia (procesor, firma, alebo iné)”. It is not marked searchable. The user selector associates the item with a Tiki account. |
| Pôvodné štatistiky a história e107 | **Hidden migration fields in the same user tracker + native Tiki user fields where appropriate** | Keep original e107 ID, registration date, last visit, visits, posts and comments as legacy values. These are not marked public. Do not overwrite Tiki's own login/activity timestamps with historical values. Validate post/comment totals against imported content before displaying any totals. |
| Súkromné správy | **Tiki Messages** | Built-in inter-user messaging. Test send, receive, unread state, permissions and email notification behavior. |
| Sledovanie noviniek a diskusií | **Tiki Watches** | Built-in user/group watches and notification preferences. Test forum and article subscriptions. |
| E-mailové notifikácie | **Tiki mail / notification settings** | Profile enables the relevant feature areas, but SMTP/from-address/cron delivery are site-specific and must be configured and tested on the hosting environment. |
| Chat | **Existing Mikrozone PHPFreeChat bridge** | Not replaced by Tiki MiniChat. The bridge already developed in Stage 2 remains a separate integration. The profile intentionally disables Tiki MiniChat to avoid two competing chat systems. |

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
- **Moja špecializácia (procesor, firma, alebo iné)** – public text area, mandatory, deliberately not searchable.
- **Pôvodné e107 ID** – private migration key.
- **Pôvodný dátum registrácie** – private legacy value.
- **Pôvodný posledný prístup** – private legacy value.
- **Pôvodný počet návštev** – private legacy value.
- **Pôvodný počet príspevkov** – private legacy value.
- **Pôvodný počet komentárov** – private legacy value.

The profile declares the specialization as a registration field for the Registered group. This must be verified in the actual Tiki 30.1 registration flow. In particular, test whether the field is shown, required, saved against the new user, and available to an administrator reviewing a pending registration. If the registration UI does not expose it correctly, do not accept that as complete: use the Tiki-supported user tracker registration integration and adjust the profile after the test.

**Important:** the legacy statistics are storage for migration fidelity, not automatically trustworthy public counters. A migration importer must preserve the source values, map e107 user IDs to Tiki user IDs, and compare post/comment totals with the content actually imported.

## Features intentionally not created by this profile

- No new chat implementation – use the Stage 2 PHPFreeChat bridge.
- No separate blog – e107 news maps to Articles.
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
