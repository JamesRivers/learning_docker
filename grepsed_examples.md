grep -h ".png" *.md >test.txt 
![](/attachments/Pasted%20image%20
![](/attachments/Pasted%20image%2020211029121711.png)
![](/attachments/Pasted%20image%2020211028174537.png)


![](/attachments/2021-10-28%2018.50.01.gif

sublines
sed -i 's:!\[](../attachments:attachments:g'
sed -i 's:%20: :g'
sed -i 's:attachments:cp "attachments:g'
sed -i 's:gif:gif" ~/Dropbox/learning_docker/attachments:g'

sed -i 's:!\[](../attachments/Pasted%20image%20:Pasted image :g'
sed -i 's:!\[](../../../attachments/Pasted%20image%20:Pasted image :g' 
sed -i 's:!\[](/attachments/Pasted%20image%20:Pasted image :g'
sed -i 's:png):png:g'
sed -i 's:Pasted:cp "Pasted:g'
sed -i 's:png:png" ~/Dropbox/learning_docker/attachments/:g'
sed -i 's:"Pasted:cp "Pasted:g'
sed- i 's:png":png" ~/Dropbox/learning_docker/attachments/:g'

File internal
sed -i 's:!\[](../attachments:!\[](/attachments:g'
sed -i 's:!\[](../../../attachments:!\[](/attachments:g'
