# Tutorial 11

## Tutorial: Hello Minikube

1. **Compare the application logs before and after you exposed it as a Service.**
    - Sebelum di expose sbg Service, log hanya menampilkan 2 baris yaitu `Started HTTP server on port 8080` dan `Started UDP server on port 8081`. Artinya, belum ada request yang masuk sama sekali. Setelah di expose dan diakses lewat browser, log langsung bertambah dengan munculnya `GET /` setiap kali halaman dibuka. Jadi ya, jumlah log bertambah setiap kali app diakses karena setiap HTTP request tercatat di log pod tersebut.

2. **What is the purpose of the -n option?**
    - Option `-n` digunakan untuk menentukan namespace yang ingin ditampilkan. Kubernetes memisahkan resource ke dalam namespace yang berbeda, komponen internal Kubernetes seperti metrics-server dan coredns berada di namespace `kube-system`, sedangkan resource yang kita buat sendiri seperti `hello-node` berada di namespace default. Karena itu, saat pakai `-n kube-system`, pod yang kita buat tidak muncul karena mereka berada di namespace yang berbeda.