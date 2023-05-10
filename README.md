# Git bash command cheatsheet

**Collections of useful git bash command**

*--apply gitignore untuk project yang udah kadung kotor*
    
    git rm -r --cached .
    git add .
    git commit -m "fixed untracked files"

*--turn off SSL verification*
    
    git config --global http.sslVerify false

*--Clone repo*

langsung dari master :
    
    git clone [link repo]

clone spesifik branch :
    
    git clone -b [nama branch] [link repo]

clone ke spesifik folder (... sama kaya command diatas, tambahin aja nama folder dibelakang link repo) :
    
    ... [link repo] [nama folder]

*--Cek status working directory saat ini*

    git status
    
*--Pull / sync dengan suatu branch*

	git pull origin [nama branch]

kalau saat push udah set-upstream/ -u, langsung aja command kaya dibawah :

    git pull
    
notes :

    - saat pull ke suatu branch lain, misal ke release, terus ternyata ada file baru di release, nanti otomatis bakal merge dengan branch yg skrng lg lo kerjain
        biasanya bakal lsg muncul vim editor, kalo gk ada yang mau diubah langsung save aja filenya dengan cara ketik :wq trus enter
    - sama kaya diatas, tapi ternyata ada file yg lg lo kerjain itu isinya beda sama yg di branch lo pull, nanti bakal conflict, buat resolvenya bisa liat dibawah
  
*--Ganti branch / create branch baru*

Kalo branchnya udh ada bakal switch kesitu, kalo blm ada, saat push nanti bakal create branch baru :

    git checkout -b [nama branch]
    
Kalo butuh clone / rollback ke posisi repo saat commit tertentu :

    git checkout -b [nama branch baru bebas] [commit sha id]
    
*--Init suatu folder jadi repo git*

    git init
    
*--Nambahin remote origin*

    git remote add origin [url repo]
    
*--Show current remote origin*

    git remote show origin

*--Ubah url remote origin*

    git remote set-url origin [url repo]

*--Commit perubahan*

buat add file yang diubah :

    git add .

kalo mau cuman add spesifik file :

    git add [nama file]

commit yang di add :

    git commit -m [message]

kalo salah input commit message terus mau ulang lagi :

    git reset --soft HEAD~1
    
kalo mau balik ke commit sebelumnya (file yg udah lo ubah bakal keapus lagi) :

    git reset --hard HEAD~1
    
kalo file udah keapus karna balik ke commit sebelumnya, ternyata lu pengen tuh file balik lagi :

    git reflog (nanti bakal munculs list commit lu beserta sha id nya)
    git checkout -b [nama branch baru bebas] [commit sha id]

*--Push perubahan*

Push perubahan setelah ngelakuin commit :

    git push origin [nama branch]

Push perubahan sekalian set-upstream origin :

    git push -u origin [nama branch]

Kalo udah set-upstream kaya diatas, kalo mau push tinggal pake command :

    git push (otomatis bakal push ke branch yang sebelumnya lo set)

Kalo ternyata saat push ada conflict, trus yaudah lo mau rebase / replace aja itu yang skrng ada di remote nya (ini kalo lo ngerjain branch itu bareng orang lain, minta consent dulu jangan asal force) :

    git push origin [nama branch] --force
    
*--Merge branch dengan suatu branch di remote*

    git checkout [nama branch 1 (branch local yg lg dikerjain)]
    git merge [nama branch 2 (branch yg mau di merge, biasanya release/master)]

atau bisa jadi pake command ini :

    git checkout [nama branch 2] [nama branch 1]

*--Rebase*

    git checkout [nama branch 1 (branch local yg lg dikerjain)]
    git rebase [nama branch 2 (branch yg mau di rebase, biasanya release/master)]
    
notes : 

    - Jangan rebase branch yang dipake bareng", contoh branch master atau release, bikin pusing nanti karna nanti developer lain butuh merge lagi dengan branch hasil rebasenya
    - kalo merge itu bakal ngegabungin branch, nah rebase itu alternatifnya, dia bakal ngegabungin 2 branch jadi 1 juga, tapi history commitnya jadi ngurut, misal :
        - ada branch 1 dengan commit a-b-c
        - branch 2 itu hasil clone dari branch 1, trus ngelakuin commit d-e
        - eh ada orang lain yg lsg push ke branch 1, jd commitnya berubah jadi a-b-c-d
        - si branch 2 tadi bisa aja ngemerge sama branch 1, tapi nanti history commitnya jadi begini : a-b-c-d-e (e isi nya commit d-e nya si branch 2)
        - nah bisa aja si branch 2 ngelakuin rebase, jd begini : a-b-c-d-e-f (commit d-e nya si branch 2 berubah jadi commit e-f)
    - hasilnya history commitnya bakal linear terus, tapi kalo semua orang ngerebase, ya bakal ke replace mulu itu si branchnya, 
      jadi rebase cocok dilakuin kalo emng ada 1 developer yang lg ngerjain fitur di suatu repo, trus trnyata ada yg ngelakuin hotfix di branch master nya, 
      nah nanti pas deploy si developer ini mending rebase aja jadi historynya lebih rapih dibanding merge  

*--Resolve conflict*

coba jalanin command ini :

    - git mergetool
    - nanti bakal muncul vimdiff yang displaynya kaya begini :
    ╔═══════╦══════╦════════╗
    ║       ║      ║        ║
    ║ LOCAL ║ BASE ║ REMOTE ║
    ║       ║      ║        ║
    ╠═══════╩══════╩════════╣
    ║                       ║
    ║        MERGED         ║
    ║                       ║
    ╚═══════════════════════╝
    LOCAL – Ini file yang ada di local lo dr branch yg lg lo kerjain
    BASE – kondisi file sebelum diubah
    REMOTE – Ini file yang lo tarik pake command pull
    MERGED – result mergenya
    kalo mau ngedit", cara navigasinya buat pindah" dari local, base, remote, merged, pencet ctrl+w
    - kalo mau ambil changes dari remote pake command :diffg REMOTE
      kalo mau ambil changes dari local pake command :diffg LOCAL
      kalo mau ambil changes dari base pake command :diffg BASE
    - :wqa (buat save and exit)
    - git commit -m "message"
    - git clean (buat ngapus extra files yang di create diff tool)
    - git push (kalo udh kelar)
