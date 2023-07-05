"secret.yaml" dosyasını burada "mysecret-pod.yaml" dosyası için referans dosyası olarak kullanmaktayız. Burada dikkat edilecek nokta "secret.yaml" dosyası içerisindeki "username" ve "password" value değerleri "base64" ile şifrelendikten sonra "secret.yaml" dosyasına referans "key=value" değerleri olarak girilmelidir. Aksi halde "base64" ile code edilmeyen şifreleri kubernetes kabul etmemektedir.

NOT:
Buradaki "mysecret-pod-env.yaml" dosyası "env" variablelarda container içerisine "key=value" şeklindeki atama yaparak kullanımını göstermek için eklenmiştir. 

====="secret.yaml"=====
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  username: YWRtaW4=          #(base64 olarak çevrilmiş şifre)
  password: MWYyZDFlMmU2N2Rm  #(base64 olarak çevrilmiş şifre)

  # echo -n 'admin' | base64 (şifreleme)
  # echo -n 'encoded_password' | base64 --decode (şifre çözme)