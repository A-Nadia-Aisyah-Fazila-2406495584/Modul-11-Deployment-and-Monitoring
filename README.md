# Tutorial 11

## Tutorial: Hello Minikube

1. **Compare the application logs before and after you exposed it as a Service.**
    - Sebelum di expose sbg Service, log hanya menampilkan 2 baris yaitu `Started HTTP server on port 8080` dan `Started UDP server on port 8081`. Artinya, belum ada request yang masuk sama sekali. Setelah di expose dan diakses lewat browser, log langsung bertambah dengan munculnya `GET /` setiap kali halaman dibuka. Jadi ya, jumlah log bertambah setiap kali app diakses karena setiap HTTP request tercatat di log pod tersebut.

2. **What is the purpose of the -n option?**
    - Option `-n` digunakan untuk menentukan namespace yang ingin ditampilkan. Kubernetes memisahkan resource ke dalam namespace yang berbeda, komponen internal Kubernetes seperti metrics-server dan coredns berada di namespace `kube-system`, sedangkan resource yang kita buat sendiri seperti `hello-node` berada di namespace default. Karena itu, saat pakai `-n kube-system`, pod yang kita buat tidak muncul karena mereka berada di namespace yang berbeda.

## Tutorial Rolling Update & Kubernetes Manifest File

1. **What is the difference between Rolling Update and Recreate deployment strategy?**
    - Rolling Update mengupdate pod dengan bertahap. Pod lama dihapus sedikit sambil pod barunya dijalankan, jadi aplikasi tetap bisa diakses selama proses update berlangsung (zero downtime). 
    - Recreate langsung mematikan semua pod lama sekaligus sebelum pod baru dibuat, jadi ada jeda waktu di mana aplikasi tidak bisa diakses sama sekali. 
    - Rolling Update lebih aman untuk production, Recreate lebih simple tapi berisiko downtime.

2. **Try deploying the Spring Petclinic REST using Recreate deployment strategy and document your attempt.**
    - Untuk menggunakan strategi Recreate, perlu mengubah bagian strategy di manifest file menjadi:
    ```
    strategy:
        type: Recreate
    ```
    Setelah apply manifest tersebut dan dilakukan update image, semua pod lama langsung terminated sekaligus sebelum pod baru dibuat. Selama proses itu aplikasi tidak bisa diakses, berbeda dengan Rolling Update yang tetap available.
    - Output `kubectl get pods -w`:
    ```
    spring-petclinic-rest-695db76b69-ccsvl   1/1     Terminating   0          12m
    spring-petclinic-rest-695db76b69-l6css   1/1     Terminating   0          12m
    spring-petclinic-rest-695db76b69-nbpzp   1/1     Terminating   0          12m
    spring-petclinic-rest-695db76b69-xrwz6   1/1     Terminating   0          12m
    spring-petclinic-rest-69cbbd78b-lxsl5    0/1     Pending       0          0s
    spring-petclinic-rest-69cbbd78b-g6gjd    0/1     Pending       0          0s
    spring-petclinic-rest-69cbbd78b-dvc52    1/1     Running       0          32s
    spring-petclinic-rest-69cbbd78b-g6gjd    1/1     Running       0          34s
    ```

3. **Prepare different manifest files for executing Recreate deployment strategy.**
    - Buat file baru `deployment-recreate.yaml` dengan mengubah bagian strategy di `deployment.yaml`:
    ```
    strategy:
        type: Recreate
    ```
    Hapus bagian rollingUpdate karena tidak relevan untuk strategi Recreate.

4. **What do you think are the benefits of using Kubernetes manifest files?**
    - Waktu deploy manual pakai `kubectl create` dan `kubectl expose` satu-satu itu lumayan ribet, apalagi jika ada typo atau salah flag harus diulang dari awal. Dengan manifest file, konfigurasi deployment dan service sudah tersimpan semua di file YAML, jadi tinggal `kubectl apply -f` sekali jalan dan semuanya langsung kesetup. Selain itu manifest file bisa dicommit ke Git, jadi konfigurasinya terdokumentasi dan bisa direuse kapanpun tanpa harus mengingat perintahnya lagi.