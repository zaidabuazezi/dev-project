# دليل التطبيق العملي: DriveHub مع Git وGitHub

هذا موقع HTML وCSS للتدريب، وليس متجرًا حقيقيًا. افتحه في المتصفح، وخلي VS Code
والتيرمنال بجانبه حتى يشوف الطلاب أثر التعديل وأثر أمر Git بشكل منفصل.

## كيف تبدأ؟

1. فكّ ZIP، وافتح مجلد `git-classroom-demo` نفسه في VS Code.
2. افتح `index.html` في المتصفح أو Live Server.
3. افتح Terminal داخل هذا المجلد. أوامر Git تعمل في PowerShell وGit Bash وMac.
4. احفظ الملف بـ Ctrl+S بعد كل تعديل، ثم حدّث المتصفح إذا لم تستخدم Live Server.
5. الأسماء المكتوبة بين `< >` في الأمثلة تُستبدل بقيم حقيقية؛ لا تكتب الأقواس.

كل تجربة رئيسية تبدأ من نقطة اسمها `lab-start`، لذلك يمكنك شرحها بشكل مستقل.
لا تنتقل للتجربة التالية إلا بعد Commit أو Stash أو إلغاء التعديلات المقصودة.
لإعادة الدرس كله، فكّ ZIP الأصلي في مجلد جديد؛ لا يوجد `.git` داخل التحميل.

## خريطة التعديلات في الموقع

| المكان | التعديل الذي تطبقه | الأوامر المناسبة |
|---|---|---|
| `Contcat us` في HTML | تصحيحها إلى `Contact us` | add, commit, diff, revert, cherry-pick |
| `--accent` في CSS | أزرق إلى أخضر أو برتقالي | diff, restore, branch, merge, rebase |
| عنوان `Find your next car` | عنوانان مختلفان على فرعين | merge conflict, merge --abort |
| السعر `$12,000` | إدخال سعر خاطئ ثم اكتشافه | log, show, bisect, revert |
| تعليق `LAB-CARD` | إضافة بطاقة سيارة ثالثة | feature branch, commit, merge, PR |
| تعليق `LAB-NAV` | إضافة رابط Contact | atomic commits, stash, rebase |
| فقرة footer | تحديث النص | commit مستقل، rebase تفاعلي |
| `.gitignore` | تجاهل `.env` وملفات log | status, check-ignore, rm --cached |
| ملف README | تعديل التوثيق | blame, log -p, cherry-pick |

## 1. الإعداد وإنشاء المستودع

اكتب اسمك وإيميلك أنت، ولا تضع كلمة السر في `git config`:

```bash
git --version
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
git config --list
git init -b main
git status
git add .
git diff --staged
git commit -m "feat: Add DriveHub starter website"
git tag lab-start
```

`lab-start` اسم Tag تعليمي لنقطة البداية. `--global` يطبق الإعداد على مستودعات
حسابك بالجهاز، بينما `git init -b main` يحدد فرع البداية لهذا المشروع.
إذا ظهر عرض طويل وانتهى بـ `(END)`، اضغط `q` للرجوع للتيرمنال.

## 2. Working Directory وStaging وCommit

ابدأ تجربة مستقلة:

```bash
git switch -c lab-staging lab-start
```

صحّح `Contcat us` إلى `Contact us` في HTML. ثم غيّر `--accent` في CSS إلى
`#16a34a`. احفظ الملفين، وشاهد الموقع.

```bash
git status
git diff
git add index.html
git diff --staged
git diff
git commit -m "fix: Correct contact heading typo"
git status
```

الآن تصحيح HTML محفوظ، لكن تغيير CSS لم يدخل بالـCommit.
`git diff` يعرض التعديلات غير المجهزة، و`git diff --staged` يعرض المجهزة.

أكمل حفظ تغيير اللون بشكل مستقل:

```bash
git add style.css
git commit -m "style: Use green accent color"
git log --oneline
```

هيك شرحت Atomic Commits: تصحيح النص لوحده، وتغيير التصميم لوحده.
وللتجهيز الجزئي داخل ملف واحد: عدّل فقرتين متباعدتين في HTML ثم استخدم
`git add -p index.html`، واقرأ خيارات `y` و`n` و`s` المعروضة. إذا جمع Git
التعديلين في جزء واحد، جرّب `s` لتقسيمه. افحص `git diff --staged` قبل الحفظ.

## 3. رسالة Commit طويلة وAmend

على نفس الفرع، غيّر فقرة footer واحفظها:

```bash
git add index.html
git commit
```

اكتب عنوانًا، ثم سطرًا فارغًا، ثم سبب التعديل. المحرر يعتمد على إعداد جهازك.
في Vim: اضغط `i`، اكتب، ثم Esc، ثم `:wq` ثم Enter. في VS Code احفظ وأغلق
تبويب رسالة الـCommit. إذا أردت استخدام VS Code كمحرر Git وكان أمر `code`
متاحًا عندك:

```bash
git config --global core.editor "code --wait"
```

لتعديل رسالة آخر Commit محلي بعد ذلك:

```bash
git commit --amend -m "docs: Clarify the practice project footer"
```

هذا يعيد كتابة آخر Commit؛ جرّبه قبل مشاركة تاريخه مع الآخرين.
لا تضف رقم Issue وهميًا. المرجع مثل `Refs: WEB-142` يوضع فقط عند وجود المهمة.

## 4. Restore وإلغاء الـStaging

```bash
git switch -c lab-restore lab-start
```

غيّر لون `--accent` واحفظ الملف، ثم:

```bash
git add style.css
git status
git restore --staged style.css
git status
git diff
```

إلغاء التجهيز أبقى تعديل اللون بالملف. الآن ألغِ تعديل اللون نفسه:

```bash
git restore style.css
```

الأمر الأخير يتخلص من التعديل غير المحفوظ بهذا الملف. حدّث المتصفح: رجع الأزرق.

## 5. .gitignore والملفات غير المتتبعة

```bash
git switch -c lab-ignore lab-start
```

أنشئ من Explorer في VS Code ملفين: `debug.log` و`.env`.
ضع داخل `.env` قيمة تدريبية فقط مثل `DEMO_VALUE=practice`.
ملف `.env.example` موجود للتوثيق ولا يحمل سرًا حقيقيًا.

```bash
git status
git status --ignored
git check-ignore -v .env debug.log
```

الملفان متجاهلان. الآن أنشئ `notes.txt` وشاهد أنه يظهر بـ`git status`.
لتشرح أن ignore لا يوقف تتبع ملف محفوظ سابقًا:

```bash
git add notes.txt
git commit -m "docs: Add local practice notes"
```

أضف `notes.txt` إلى `.gitignore`، واحفظ. نفّذ:

```bash
git ls-files notes.txt
git rm --cached notes.txt
git add .gitignore
git commit -m "chore: Stop tracking local notes"
```

`notes.txt` بقي على الجهاز، لكن أُزيل من النسخة المتتبعة في هذا الفرع.
الملفات المتجاهلة تظل على الجهاز عند تبديل الفروع؛ لا تستخدم أسماءها لاحقًا
لملفات تتوقع وجودها في نقطة البداية. المشروع لا يقرأ `.env` فعليًا.

## 6. إنشاء الفروع والانتقال بينها

```bash
git switch main
git branch feature/contact
git branch
git switch feature/contact
```

استبدل محتوى section الاتصال بهذا الكود:

```html
<section id="contact" class="contact section">
  <h2>Contact us</h2>
  <p>Tell us which car you like.</p>
  <p>Email: <a href="mailto:sales@example.com">sales@example.com</a></p>
</section>
```

```bash
git add index.html
git commit -m "feat: Expand contact section"
git switch main
```

حدّث المتصفح: اختفى التعديل لأنه محفوظ على فرع آخر. لإعادته:

```bash
git switch feature/contact
```

`git checkout main` طريقة أخرى للانتقال. `git switch -c NEW_NAME` ينشئ وينتقل
بأمر واحد. `git branch` وحده لا ينقلك. الملفات غير المحفوظة لا تنتمي للفرع
بـCommit، وقد ترافقك أو تمنع الانتقال؛ اعمل Commit أو Stash للتوضيح.

## 7. Fast-forward Merge

هذا يكمل تجربة فرع الاتصال السابقة. لم نعمل أي Commit جديد على `main`:

```bash
git switch main
git merge feature/contact
git log --oneline --graph --decorate
```

المتوقع: Fast-forward. انتقل مؤشر `main` إلى الـCommit الموجود أصلًا على
`feature/contact`، ولم يُنشأ Merge Commit جديد. الآن التعديل ظاهر على `main`.

## 8. Three-way Merge بدون Conflict

```bash
git switch -c lab-threeway lab-start
git switch -c feature/color
```

غيّر `--accent` إلى `#f97316` في CSS ثم:

```bash
git add style.css
git commit -m "style: Use orange accent"
git switch lab-threeway
```

صحّح `Contcat us` في HTML ثم:

```bash
git add index.html
git commit -m "fix: Correct contact heading"
git merge feature/color
git log --oneline --graph --decorate
git show --no-patch --format=raw HEAD
git merge-base HEAD feature/color
```

حدث شغل على الطرفين؛ ينتج Merge Commit بأبوين. إذا فتح محرر رسالة الدمج، احفظ
وأغلقه. آخر أمر يعرض أفضل سلف مشترك للـCommitين المحددين؛ بعد الدمج صار طرف
الميزة نفسه سلفًا للنتيجة. لفحص الأساس قبل الدمج، نفّذ
`git merge-base lab-threeway feature/color` قبل `git merge`.

## 9. Conflict واضح على الشاشة

```bash
git switch -c lab-conflict lab-start
git switch -c feature/hero
```

غيّر سطر العنوان نفسه إلى:

```html
<h1>Drive your dream car</h1>
```

```bash
git add index.html
git commit -m "feat: Change hero headline"
git switch lab-conflict
```

غيّر نفس السطر إلى:

```html
<h1>Find a car for every journey</h1>
```

```bash
git add index.html
git commit -m "feat: Update hero message"
git merge feature/hero
git status
```

افتح HTML. اشرح علامات `<<<<<<<` و`=======` و`>>>>>>>`.
بدل منطقة التعارض كاملة ضع سطرًا واحدًا تختارونه بالصف، مثل:

```html
<h1>Find your dream car for every journey</h1>
```

احذف علامات التعارض نفسها، ثم:

```bash
git add index.html
git commit -m "merge: Resolve hero headline conflict"
```

إذا أردت إلغاء عملية الدمج بدل حلّها، استخدم `git merge --abort` أثناء التعارض
وقبل إنهاء الدمج. اختَر مسار الحل أو مسار الإلغاء، وليس الاثنين بعد بعض.

## 10. Stash

```bash
git switch -c lab-stash lab-start
```

عدّل فقرة About في HTML، واحفظها بدون Commit:

```bash
git stash push -m "WIP about section"
git stash list
git status
git switch main
git switch lab-stash
git stash show -p
git stash pop
git diff
```

اختفت التعديلات مؤقتًا ثم رجعت. `git stash apply` يسترجع بدون حذف سجل الـStash،
و`git stash drop` يحذف السجل. `git stash push -u` يشمل ملفات جديدة غير متتبعة؛
الـStash العادي لا يشملها. لا تشغّل apply ثم pop على نفس التعديلات بلا سبب.
احفظ شغلك أو ألغِه قبل التجربة التالية.

## 11. Revert

```bash
git switch -c lab-revert lab-start
```

غيّر اللون إلى `#dc2626`، واحفظ:

```bash
git add style.css
git commit -m "style: Try red accent"
git revert HEAD
git log --oneline -3
```

احفظ رسالة Revert إذا فتح المحرر. رجع الأزرق بـCommit جديد، والـCommit الأحمر
ظل بالتاريخ. هذا يعكس تعديلًا ولا يحذف سجل التعديل الأصلي.

## 12. Reset: كرر الخطوات لكل نوع بشكل مستقل

ابدأ فرعًا جديدًا من `lab-start` باسم مختلف لكل نوع، مثل `lab-reset-soft`.
غيّر اللون، ثم `git add style.css` و`git commit -m "style: Try new accent"`.
الآن لديك Commit واحد إضافي فوق البداية. اختَر أمرًا واحدًا:

| الأمر | تاريخ الفرع | Staging | الملفات |
|---|---|---|---|
| `git reset --soft HEAD~1` | يرجع قبل آخر Commit | التعديل يبقى مجهزًا | اللون الجديد يبقى |
| `git reset --mixed HEAD~1` | يرجع قبل آخر Commit | يُلغى التجهيز | اللون الجديد يبقى |
| `git reset --hard HEAD~1` | يرجع قبل آخر Commit | يرجع لحالة الهدف | اللون يرجع للأزرق |

افحص `git status` و`git diff` و`git diff --staged` بعد كل نوع.
استخدم hard فقط في تجربة التدريب التي تقصد التخلص من تعديلاتها.
للبدء بالتجربة التالية بعد soft/mixed، أعد حفظ تعديل اللون بـCommit أو ألغِه
عن قصد. لا تستخدم هذا المثال لإعادة كتابة تاريخ مشترك ومرفوع للفريق.

## 13. Reflog واستعادة الـCommit

بعد تجربة hard مباشرة:

```bash
git reflog
```

ابحث عن Commit تغيير اللون قبل reset، وانسخ Hash الحقيقي من الناتج.
أنشئ فرع استعادة عنده:

```bash
git switch -c recovered-color <COMMIT_HASH>
```

رجع اللون الجديد. Reflog سجل محلي، وليس نسخة احتياطية دائمة لكل شيء.

## 14. Detached HEAD

```bash
git switch --detach lab-start
git status
```

الآن HEAD يشير إلى Commit البداية مباشرة. افحص الموقع ثم ارجع:

```bash
git switch main
```

لو أردت تجربة Commit أثناء Detached HEAD: أعد الدخول، غيّر footer، واعمل add
وcommit، ثم قبل المغادرة احتفظ به بفرع:

```bash
git switch -c saved-experiment
```

## 15. Rebase بمثال واضح

```bash
git switch -c lab-rebase-base lab-start
git switch -c feature/rebase-color
```

غيّر لون CSS للأخضر ثم:

```bash
git add style.css
git commit -m "style: Use green accent"
git switch lab-rebase-base
```

صحّح خطأ Contcat في HTML ثم:

```bash
git add index.html
git commit -m "fix: Correct contact heading"
git switch feature/rebase-color
git log --oneline -3
git rebase lab-rebase-base
git log --oneline --graph --decorate
```

صار فرع الميزة يحتوي تصحيح النص ثم تعديل اللون. Hash تعديل اللون تغير؛ فرع
`lab-rebase-base` لم يتغير. عند التعارض: حلّ الملفات ثم `git add` ثم
`git rebase --continue`، أو استخدم `git rebase --abort` لإلغاء العملية.
التجربة على فروع محلية خاصة لا يعتمد عليها زملاؤك.

## 16. Interactive Rebase وSquash

```bash
git switch -c lab-interactive lab-start
```

اعمل ثلاثة Commits على التوالي، كل مرة عدّل واحفظ ثم add وcommit:

1. تصحيح Contcat في HTML.
2. تعديل وصف الاتصال في HTML.
3. تعديل footer في HTML.

```bash
git rebase -i HEAD~3
```

في قائمة الـCommits، اترك الأول `pick` وغيّر الثاني إلى `squash`، واترك الثالث
`pick`. احفظ وأغلق، وعدّل رسالة الـCommit المدموج عند طلبها.
`reword` لتغيير الرسالة، و`fixup` لدمج التغيير والتخلص من رسالته، و`drop`
لحذف Commit من التاريخ المعاد بناؤه. التجربة قبل نشر هذا التاريخ.

## 17. Cherry-pick

```bash
git switch -c lab-cherry-source lab-start
```

صحّح Contcat ثم:

```bash
git add index.html
git commit -m "fix: Correct contact heading"
git tag lab-typo-fix
git switch -c lab-cherry-target lab-start
git cherry-pick lab-typo-fix
git log --oneline -2
```

نقلت تعديل Commit محدد للفرع الهدف بدون دمج فرع المصدر كله. لا تكرر cherry-pick
على نفس التعديل الموجود أصلًا وتتوقع تغييرًا جديدًا.

## 18. Bisect: مين أدخل السعر الغلط؟

```bash
git switch -c lab-bisect lab-start
```

اعمل هذه الـCommits بالترتيب، واحفظ وadd وcommit بعد كل خطوة:

1. تعديل footer فقط؛ السعر يبقى `$12,000`.
2. تغيير سعر City Sedan من `$12,000` إلى `$120,000`؛ هذا الخطأ المقصود.
3. تعديل فقرة About فقط؛ السعر يبقى خاطئًا.
4. تغيير لون CSS؛ السعر يبقى خاطئًا.

```bash
git bisect start
git bisect bad
git bisect good lab-start
```

Git ينتقل بين نسخ المشروع. افحص سعر City Sedan في كل نسخة:

- إذا `$12,000`: نفّذ `git bisect good`.
- إذا `$120,000`: نفّذ `git bisect bad`.

كرر حتى يحدد أول Commit سيئ، ثم ارجع لوضعك الطبيعي:

```bash
git bisect reset
```

لا تعدّل الملفات أثناء البحث؛ فقط افحص وعلّم جيد/سيئ.

## 19. Log وShow وBlame وGit Objects

```bash
git log --oneline --graph --decorate --all
git log -p -- index.html
git show HEAD
git show lab-start:index.html
git blame index.html
git rev-parse HEAD
git cat-file -p HEAD
git ls-tree HEAD
git rev-parse HEAD:index.html
git cat-file -p HEAD:index.html
git fsck
```

`cat-file -p HEAD` يعرض معلومات Commit والـTree والـParent إن وجد.
`ls-tree` يوضح أسماء الملفات وHashes محتواها، و`cat-file -p HEAD:index.html`
يعرض محتوى الـBlob. `fsck` فحص لاتساق قاعدة Objects؛ تقارير dangling قد تكون
متوقعة بعد تجارب reset/rebase. `blame` يبين آخر Commit غيّر كل سطر، وليس حكمًا
على الشخص المسؤول عن الخطأ.

## 20. نقل وحذف الملفات وتنظيف غير المتتبع

```bash
git switch -c lab-files lab-start
git mv README.md ABOUT.md
git status
git commit -m "docs: Rename project overview"
git rm ABOUT.md
git commit -m "docs: Remove practice overview"
```

أنشئ ملفًا إضافيًا اسمه `throwaway.txt` من VS Code، ثم:

```bash
git clean -n
```

هذا يعرض ما سيُحذف فقط. إذا ظهر الملف التدريبي وحده، احذفه تحديدًا:

```bash
git clean -f -- throwaway.txt
```

الملفات غير المتتبعة ليست محفوظة بـCommits حتى يقدر Git استرجاعها.

## 21. حذف فرع أو تغيير اسمه

على فرع غير الفرع الذي تريد حذفه:

```bash
git branch throwaway lab-start
git branch -m throwaway renamed-demo
git branch -d renamed-demo
```

`-d` يتحقق أن تاريخ الفرع مدموج في upstream الخاص به، أو HEAD إذا لم يكن له
upstream. `git branch -D NAME` يتجاوز فحص الدمج؛ جرّبه فقط على فرع تجريبي تقصد
حذفه. حذف الفرع لا يحذف ملفات مشروعك مباشرة ولا يلغي Commits من الفروع الأخرى.

## 22. Tags وReleases

على الفرع الذي تريد اعتبار حالته إصدارًا:

```bash
git tag -a v1.0.0 -m "First classroom release"
git tag
git show v1.0.0
```

Tag يشير لنقطة معينة ولا يتقدم تلقائيًا مثل الفرع. بعد ربط GitHub:

```bash
git push origin v1.0.0
```

من GitHub افتح Releases وأنشئ Release من هذا الـTag، واكتب وصف التغييرات.
Major لتغيير غير متوافق مع الواجهة المعتمدة، وMinor لميزة متوافقة، وPatch
لإصلاح متوافق؛ سياسة ترقيم الموقع البسيط تختارها أنت وتوضحها للفريق.

## 23. GitHub: Clone وRemote وPush وFetch وPull

أنشئ مستودعًا فارغًا على GitHub بدون README أو .gitignore جديدين. استبدل
USERNAME وREPOSITORY بالقيم الحقيقية:

```bash
git switch main
git remote add origin https://github.com/USERNAME/REPOSITORY.git
git remote -v
git push -u origin main
```

المصادقة عبر HTTPS/SSH حسب إعداد حسابك؛ `user.email` ليس تسجيل دخول، ولا تضع
كلمة سر الحساب في عنوان المستودع.

من مجلد آخر خارج المشروع، أو من جهاز الطالب الثاني:

```bash
git clone https://github.com/USERNAME/REPOSITORY.git student-copy
```

افتح النسخة الثانية، عدّل README، واحفظ وcommit وpush، ثم في النسخة الأولى:

```bash
git fetch origin
git log --oneline main..origin/main
git diff main origin/main
git status
git pull --ff-only
```

Fetch يجلب التاريخ ويحدّث `origin/main`، ولا يدمجه تلقائيًا بملفات عملك.
المثال يفترض أنك لم تعمل Commit محليًا إضافيًا على `main`، لذلك pull --ff-only
ينجح. إذا تفرع التاريخ فإنه يرفض بدل اختيار طريقة الدمج عنك.

`git remote set-url origin NEW_URL` لتغيير رابط Remote، و`git remote rename`
لتغيير اسمه، و`git remote remove NAME` لفك إعداد الاتصال، وليس لحذف مستودع
GitHub نفسه. افحص الأسماء قبل تغييرها.

## 24. Pull Request ومراجعة الكود

من `main` المحدث والنظيف:

```bash
git switch -c feature/electric-car
```

انسخ بطاقة `article` داخل `.car-grid`، وغيّر الاسم إلى Electric Hatch، والسعر
إلى `$18,000`، والوصف إلى Electric power for city trips. ثم:

```bash
git add index.html
git commit -m "feat: Add electric car card"
git push -u origin feature/electric-car
```

على GitHub افتح PR نحو main، اشرح التغيير، وخلي طالبًا آخر يراجع الفرق.
بعد الدمج على GitHub، ارجع محليًا إلى main ونفّذ fetch ثم pull --ff-only.
اختر نوع الدمج بالواجهة واشرح أثره؛ Squash/Rebase بالواجهة قد لا ينتج نفس
الـHashes المحلية. لا تفترض أن `branch -d` سيعتبر الفرع مدموجًا بعد Squash.

للتدريب على Fork: الطالب الذي لا يملك صلاحية كتابة يعمل Fork، ثم Clone لنسخته،
ويعمل ميزة ويرفعها إلى Fork الخاص فيه، ويفتح PR للمشروع الأصلي.

```bash
git remote add upstream https://github.com/ORIGINAL_OWNER/REPOSITORY.git
git fetch upstream
```

`upstream` هنا اسم Remote للمشروع الأصلي، وليس هو نفسه مفهوم upstream tracking
الذي يضبطه `git push -u`.

## 25. حماية الفرع وIssues وActions: على GitHub

- أنشئ Issue: إضافة بطاقة سيارة كهربائية، ثم اربط PR الحقيقي بها.
- استخدم Labels وProjects لتنظيم المهام.
- اضبط حماية `main` من إعدادات GitHub بحسب الميزات المتاحة لحسابك: مراجعة PR
  وفحوصات مطلوبة. كتابة `branch.main.protect=true` في config لا تنشئ حماية Git.
- ضع Workflow تجريبيًا في `.github/workflows/check.yml` لتشغيل فحص على Pull
  Requests وPush. هذا مثال مستقل يثبت وجود الملفين فقط؛ ليس اختبارًا لصحة HTML:

```yaml
name: Website files
on: [push, pull_request]
jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Check starter files
        run: |
          test -s index.html
          test -s style.css
```

لا يلزم هذا الملف لتشغيل الموقع. قبل استخدام Workflow بإنتاج حقيقي، راجع
توثيق GitHub الحالي وثبّت اعتماداته وفق سياسة فريقك. Secrets الخاصة بالأتمتة
تُضبط بإعدادات GitHub، ولا تُكتب في HTML أو ملفات YAML.

## 26. Worktree وLFS وSubmodule: أدوات اختيارية

بعد جعل مجلد عملك نظيفًا، ومن داخل مستودع التدريب:

```bash
git worktree add -b lab-preview ../drivehub-preview lab-start
git worktree list
git worktree remove ../drivehub-preview
```

هذا ينشئ مجلد عمل آخر مرتبطًا بنفس المستودع. افتحه بمتصفح ثانٍ لتقارن نسختين.
remove يحتاج أن يكون المجلد بلا تعديلات تريد الاحتفاظ بها.

LFS يحتاج Git LFS مثبتًا. للتجربة فقط ضع صورة أو ملفًا كبيرًا فعليًا بالمسار
المذكور ثم:

```bash
git lfs install
git lfs track "*.psd"
git add .gitattributes
```

الأمر يحدد نمط تتبع الملفات المستقبلية؛ لا يعيد كتابة ملفات قديمة تلقائيًا.
لا يحتاج موقعنا LFS لأنه لا يحتوي ملفات كبيرة.

Submodule يحتاج مستودعًا آخر حقيقيًا تملك حق قراءته؛ على فرع تجريبي منفصل:

```bash
git submodule add https://github.com/OWNER/ANOTHER_REPOSITORY.git shared
git submodule status
git add .gitmodules shared
git commit -m "chore: Add shared repository"
```

هذا يحفظ مرجعًا لـCommit من المستودع الآخر. استبدل الرابط ولا تستخدم المثال
كما هو. لا يلزم Submodule لهذا الموقع.

## أهم شيء بالشرح للطلاب

قبل كل أمر اسأل: «شو حالة الملفات الآن؟ وشو متوقع يتغير بعد الأمر؟»
وبعده اعرض `git status`، والفرق أو الرسم، وحدّث المتصفح عند الحاجة.
Commit لا ينشر الموقع على الإنترنت، وPush يرفع الكود ولا يعني نشر الموقع
تلقائيًا. HTML وCSS يظهروا أثر تعديل الكود؛ Git يشرح كيف تحفظ هالتعديلات
وتشاركها وتسترجعها.
