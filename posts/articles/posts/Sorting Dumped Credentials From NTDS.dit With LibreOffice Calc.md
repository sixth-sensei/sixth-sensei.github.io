# Sorting Dumped Credentials From NTDS.dit With LibreOffice Calc

***

After dumping the **NTDS.dit** secrets we have to sort out the **NT** part of this hashes that where dumped before we can crack them 👾

![](https://i.imgur.com/xd3Tc1Y.png)

We can use a Tool just like **MS EXCEL** called **LibreOffice Calc** to do this, Launch the tool and let do this ✈️

- [ ] Copy the whole part of these hashes

![](https://i.imgur.com/5N56Q7h.png)


- [ ] Paste it into **LibreOffice Calc** and you should see this prompt

![](https://i.imgur.com/ZEyVDgT.png)

- [ ] In the **Separator Options** menu Click **Separated by >> Other** then add a `:` and click **Ok**

![](https://i.imgur.com/PxaBYng.png)

- [ ] Now highlight this two columns as shown below and hit the **Delete** key on your keyboard, since we don't need them

**_Before :_**

![](https://i.imgur.com/1pEFS8m.png)

**_After :_**

![](https://i.imgur.com/E3KGZtf.png)

- [ ] Now save these hashes in a `.txt` file without the usernames and crack them with the syntax as shown below

```powershell
# Cracking NT hashes with hashcat
$ hashcat -m 1000 hash.txt /usr/share/wordlists/rockyou.txt -O

# Show cracked hashes
$ hashcat -m 1000 hash.txt /usr/share/wordlists/rockyou.txt -O --show
```

- [ ] Now copy the cracked hashes and create a New sheet on **LibreOffice Calc** , then paste it there.

![](https://i.imgur.com/odxoJ6z.png)

![](https://i.imgur.com/OOq0d62.png)


## **Performing a Vertical Lookup**

Now we want to lookup these hashes on each table we can watch this video to understand better


