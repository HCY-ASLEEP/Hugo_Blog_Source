This is my hugo source markdown files.

```bash
.
├── drafts
│   ├── archetypes
│   ├── assets
│   ├── config
│   ├── content
│   ├── data
│   ├── .hugo_build.lock
│   ├── layouts
│   ├── public
│   ├── resources
│   ├── static
│   └── themes
├── .git
│   ├── branches
│   ├── COMMIT_EDITMSG
│   ├── config
│   ├── description
│   ├── HEAD
│   ├── hooks
│   ├── index
│   ├── info
│   ├── logs
│   ├── objects
│   └── refs
├── .gitignore
├── hugo
│   ├── hugo
│   ├── LICENSE
│   └── README.md
├── README.md
└── source
    ├── archetypes
    ├── assets
    ├── config
    ├── content
    ├── data
    ├── .hugo_build.lock
    ├── layouts
    ├── public
    ├── resources
    ├── static
    └── themes

31 directories, 12 files
```
---------------------

Quikly config blogs source files:

```bash
git config --global init.defaultBranch main;\
git config --global user.name "hcy-asleep";\
git config --global user.email 2420066864@qq.com;\
git config  --global credential.helper store;\
git clone https://github.com/HCY-ASLEEP/Hugo_Blog_Source.git;\
mv Hugo_Blog_Source/ blogs/;\
mkdir blogs/source/themes/;\
git clone https://github.com/adityatelange/hugo-PaperMod.git blogs/source/themes/PaperMod/;\
mkdir blogs/source/public/;\
cp blogs/source/autogit blogs/source/public/autogit;
cd blogs/source/public/;\
git init;\
git remote add origin https://github.com/HCY-ASLEEP/HCY-ASLEEP.github.io.git;\
curl https://raw.githubusercontent.com/HCY-ASLEEP/HCY-ASLEEP.github.io/main/google188014ab03dc55b7.html -o google188014ab03dc55b7.html;\
cd ..
```
