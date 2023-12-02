---
title: "Multiple ssh for github"
seoTitle: "add multiple ssh keys"
datePublished: Sat Dec 02 2023 17:59:06 GMT+0000 (Coordinated Universal Time)
cuid: clpocy9c3000008i91pr8epnb
slug: multiple-ssh-for-github
tags: devops, ssh, ssh-keys, ssh-git

---

Hey there, fellow code wranglers! Today, I'm going to spill the beans on a little trick that saved my bacon during my early developer internships. Picture this: you're happily using your personal GitHub account with SSH, and boom! Suddenly, you need to juggle multiple SSH keys. Sound familiar?

#### Step-by-Step Guide to Adding Your First SSH Key (Yes, it's a breeze!)

1. **Fire up that Terminal:**
    
2. **Generate your SSH key:** Replace `<your_email_id>` with your GitHub email, `<id_rsa_name>` with a name you want for your RSA file and run this nifty little command :
    
    ```bash
    ssh-keygen -t rsa -b 4096 -C “<your_email_id>” -f ~/.ssh/id_rsa_name
    ```
    
3. **Start your ssh-agent:** This might vary based on your setup, but generally, you can kick it off with:
    
    ```bash
    eval "$(ssh-agent -s)"
    ```
    
    (Pro tip: You need to do this only once.)
    
4. **Add that SSH key to your ssh-agent:** Replace `id_rsa_name` if yours is different. Here's the command:
    
    ```bash
    ssh-add ~/.ssh/id_rsa_name
    ```
    
    **Config time:** Edit your SSH config file like so: Replace name in `Host` accordingly
    
    ```bash
    cat >> ~/.ssh/config << SSHConfigEoF  
    Host name.github.com  
    HostName github.com
    PreferredAuthentications publickey  
    IdentityFile ~/.ssh/id_rsa_name 
    SSHConfigEoF`
    ```
    
5. **Hook it up with GitHub:** Add your SSH key to your GitHub account. Check out their guide if you need a hand. [adding ssh to Github](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)
    
6. **Rinse and Repeat for More Keys:** When cloning new repos, use a slight twist in the command: Assuming your repo link is `git clone` [`git@github.com`](mailto:git@github.com)`:<user>/<repo_name>` replace with `git clone` [`git@name.github.com`](mailto:git@name.github.com)`:<user>/<repo_name>` . `name` is the `Host` you gave in step `5`
    

#### Quick Security Check: Permissions Matter!

* **Config file (**`rw-r--r-`): You can read and write; others can just peek.
    
* **Private key (**`id_rsa`, `rw-------`): Eyes only for you – full access, but keep it under wraps!
    
* **Public key (**`id_`[`rsa.pub`](http://rsa.pub), `rw-r--r--`): You can read and write; others can just peek.
    

---

There you have it, folks – a quick trip through the world of multiple SSH keys. Whether you're a seasoned dev or just dipping your toes in the tech pool, remember: there's always a way around those pesky tech hurdles.

[generate ssh token](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent?platform=linux)

---

### Connect with Me

* Email: [sebinsebzz2002@gmail.com](mailto:sebinsebzz2002@gmail.com)
    
* GitHub: [github.com/sebzz2k2](http://github.com/sebzz2k2)