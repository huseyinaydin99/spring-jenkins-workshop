# my-spring-boot-jenkins-workshop

![jenkins](https://github.com/huseyinaydin99/my-spring-jenkins-workshop/assets/16438043/0cd321e8-abc5-4543-985f-a662298b2f27)
![jenkins2](https://github.com/huseyinaydin99/my-spring-jenkins-workshop/assets/16438043/54dada3a-e60b-4498-aabb-dec31cbd8f26)


### 🧬 Jenkins ile CI/CD Yolculuğum 🚀

Bu dokümanda, kendi projelerimde aktif olarak kullandığım **Jenkins + CI/CD** yaklaşımını, hem teorik tarafıyla hem de günlük pratikte işime nasıl yaradığını, bir geliştirici gözüyle derli toplu şekilde anlatmaya çalışıyorum.  
Her bölümde; “**nedir / ne değildir**”, “**neden kullanıyorum**”, “**kullanmazsam başıma neler gelebilir**”, “**hangi senaryolarda tercih ediyorum**” ve “**bana ne katıyor**” sorularına bilinçli cevaplar arıyorum. 😎  

---

#### 1️⃣ Jenkins’e Giriş, Mimari ve Temel Kavramlar 🏗️

Jenkins’i ben, yazdığım kodu sürekli olarak derleyen, test eden, paketleyen ve istenirse otomatik olarak hedef ortama gönderen, arka planda sabırla çalışan otomasyon orkestratörü olarak görüyorum; bu açıdan bakınca Jenkins aslında tek başına bir amaç değil, **CI/CD süreçlerimi yaşayan, ölçülebilir ve tekrar edilebilir hale getiren** bir araç oluyor. 💽  

- Jenkins, temelde **master/controller + agent/node** mimarisiyle çalışan, HTTP tabanlı bir web arayüzü üzerinden yönetilen, job ve pipeline kavramları etrafında şekillenen bir otomasyon sunucusudur; bu yapı sayesinde farklı ortamlardaki build ajanlarını tek bir yerden yöneterek, CPU ve kaynak tüketimini ölçeklenebilir şekilde kontrol edebiliyorum. ⚙️  
- “Jenkins nedir, ne değildir?” diye baktığımda, Jenkins’in bir **build server + orkestrasyon aracı** olduğunu ve tek başına bir “deployment aracı” ya da “konfigürasyon yönetim sistemi” olmadığını net biçimde görüyorum; yani Jenkins, Ansible / Kubernetes / Terraform gibi araçları da tetikleyebilen üst seviye bir koordinatör rolüne sahipken, altyapının kendisi olma iddiasında değil. 🧭  
- Jenkins kullanmadığım bir dünyada, her commit sonrası manuel build almak, testleri tek tek çalıştırmak, paketleri elle kopyalamak ve deployment adımlarını terminalden veya uzak sunuculardan yönetmek zorunda kalacağımı biliyorum; bu da hem insan hatasına çok açık hem de zaman ve motivasyon açısından sürdürülebilir olmayan bir süreç yaratıyor. ⏳  
- CI/CD tarafında Jenkins’i özellikle, **takımın commit frekansı arttığında, branch’ler çoğaldığında ve ortama düzenli releaseler yapmam gerektiğinde** tercih ediyorum; çünkü bu koşullarda manuel süreçler hem gecikmelere hem de “çalışıyordu ama prod’da patladı” senaryolarına davetiye çıkarıyor. 🚨  
- Yazılım geliştirici olarak Jenkins bana; **güvenli bir geri bildirim döngüsü (feedback loop)**, her push sonrasında koşan otomatik testler, code quality kontrolleri ve release’lere karşı çok daha özgüvenli olma özgürlüğü kazandırıyor; böylece “deploy günü kabusu” yerine “küçük, sık ve güvenli üretim geçişleri” yaşayabiliyorum. 🌱  

#### Temel Kavramlar: Job, Pipeline, Agent, Workspace 🧩

- **Job**: Jenkins’te job dediğim şey, belirli bir işi baştan sona tarif ettiğim, build adımlarını, testleri ve artefact üretimini barındıran temel çalıştırılabilir birimdir; ister freestyle job ister pipeline job olsun, aslında her biri belirli bir senaryoyu otomatikleştiren mini bir süreç tasviri gibi düşünülebilir. 📦  
- **Pipeline**: Pipeline, CI/CD sürecimi “stage” ve “step” kavramlarıyla kod haline getirdiğim, versiyon kontrolüne koyabildiğim, değişiklikleri gözüm kapalı takip edebildiğim tanım dosyasıdır; bu sayede Jenkins ayarlarım GUI’de kaybolmak yerine, doğrudan repository içinde yaşayabiliyor. 📜  
- **Agent/Node**: Agent’lar, build süreçlerimi koşan fiziksel veya sanal makinelerdir; ana Jenkins controller sadece orkestrasyon yaparken, asıl CPU’yu harcayan job çalıştırma kısmını bu agent node’lar üstlenir, bu da bana hem ölçeklenebilirlik hem de izole ortamlar tasarlama imkanı verir. 🖥️  
- **Workspace**: Her job çalıştığında, ilgili agent üzerinde bir çalışma alanı (workspace) oluşturulur; bu alan içinde kaynak kodum klonlanır, derleme ve test çıktıları üretilir ve job tamamlandığında workspace’i temizlemek veya saklamak tamamen benim tercihime kalır. 🧹  

---

### 2️⃣ Kurulum, Konfigürasyon ve Jenkins Dashboard Yönetimi 🧰

İlk kurulum ve dashboard yönetimi, Jenkins ile olan ilişkimi ya kolaylaştıran ya da uzun vadede beni yoran kritik bir adım; bu yüzden hem **kurulumu basit ama kontrollü** yapmaya çalışıyorum hem de ilk günden bazı temel best practice’leri uygulamayı alışkanlık haline getiriyorum. 🔧  

- Jenkins’i ister Docker ile ister klasik war paketi veya native paketler üzerinden kurarken, hedefim her zaman **tekrarlanabilir bir kurulum reçetesi** çıkarmak oluyor; çünkü bir süre sonra staging için ayrı, PoC için ayrı, prod için ayrı Jenkins instanceları oluşturmak gerekebileceğini biliyorum ve bu durumda dokümante edilmemiş kurulum adımları büyük risk oluşturuyor. 📚  
- Özellikle Docker ile Jenkins çalıştırdığım senaryolarda, volume’lar üzerinden `JENKINS_HOME` dizinini kalıcı hale getirmek, plugin ve job konfigürasyonlarının container silinse bile kaybolmamasını sağlamak açısından çok kritik; aksi halde her container restart’ında yeniden kurulum yapar gibi uğraşmak zorunda kalabilirim. 🧱  
- Dashboard tarafında, “**her şeyi tek Jenkins instance içinde yapayım**” yaklaşımı yerine, projelere göre view’lar ve folder yapıları oluşturarak hem kendim hem de ekip arkadaşlarım için daha okunabilir bir ekran dizayn etmeye önem veriyorum; bu sayede yüzlerce job içinde kaybolmak yerine, proje bazlı izlenebilirlik elde ediyorum. 🗂️  
- Jenkins’in global konfigürasyon ekranında, **JDK, Maven, Gradle, Node.js** gibi araçların path tanımlarını sistem-wide şekilde yapmak, job bazında tekrar eden konfigürasyon yükünü azaltıyor; böylece her yeni pipeline oluşturduğumda sadece ilgili tool’ı seçmem yeterli oluyor. 🛠️  
- Kurulum aşamasında güvenlik tarafını boş verip sadece admin/admin ile devam etmek kısa vadede cazip görünse de, uzun vadede audit ihtiyacı ortaya çıktığında, “kim ne zaman hangi job’ı tetikledi, hangi değişikliği yaptı?” sorularına cevap verememenin beni zor durumda bırakacağını bildiğim için, daha en başta kullanıcı ve rol yönetimini düşünmeye çalışıyorum. 🧱  

#### Örnek: Docker ile Hızlı Jenkins Kurulumu 🐳

Aşağıda kendi makinemde kullandığım, basit ama iş görür bir Jenkins Docker Compose örneği bulunuyor; bu yapıyı, ihtiyaç oldukça plugin yedekleri ve reverse proxy ekleyerek zenginleştiriyorum:

```yaml
version: "3.8"

services:
  jenkins:
    image: jenkins/jenkins:lts-jdk17
    container_name: my-jenkins
    restart: unless-stopped
    ports:
      - "8080:8080"
      - "50000:50000"
    volumes:
      - jenkins_home:/var/jenkins_home
    environment:
      - JAVA_OPTS=-Xms512m -Xmx1024m

volumes:
  jenkins_home:
```

Bu yapı ile Jenkins’i ayağa kaldırdıktan sonra, tarayıcıdan `http://localhost:8080` adresine gidip ilk admin şifresini girdikten sonra, plugin kurulum ve kullanıcı oluşturma adımlarına geçiyorum. 🌐  

---

### 3️⃣ Jenkins Job’ları, Pipeline (Declarative & Scripted) ve Stages 🧪

Jenkins tarafında asıl kasımı gösterdiğim yerin job ve pipeline tasarımı olduğunu söyleyebilirim; çünkü CI/CD akışımın ne kadar okunabilir, yönetilebilir ve genişletilebilir olacağı doğrudan bu aşamada aldığım kararlara bağlı oluyor. 💡  

- Freestyle job’lar bana hızlı PoC’ler ve basit cron çalıştırmaları için büyük konfor sağlasa da, uzun vadede asıl gücü **pipeline as code** yaklaşımıyla dekleratif pipeline’larda gördüğümü itiraf etmem gerekiyor; çünkü Jenkinsfile ile versiyon kontrolüne giren pipeline tanımları, hem kod review süreçlerine dahil edilebiliyor hem de ortamlar arası taşınması çok daha kolay hale geliyor. 📜  
- Declarative pipeline bana, **daha okunabilir, daha az sürprizli ve kural tabanlı bir yapı** sunarken, scripted pipeline ise Groovy’nin esnekliğini getirdiği için karmaşık orkestrasyon senaryolarında işime yarıyor; bu nedenle projelerimde çoğunlukla declarative pipeline tercih etsem de, gerektiğinde scripted pipeline ile ileri seviye kontrol mekanizmaları kurabiliyorum. 🧠  
- Stages kavramı, build sürecimi akış içinde görsel olarak bölümlere ayırmama yardımcı oluyor; örneğin `Checkout`, `Build`, `Test`, `Package`, `Docker Build`, `Deploy` gibi stage’ler sayesinde hem log okumak hem de başarısız olan adımı nokta atışı tespit etmek inanılmaz kolaylaşıyor. 🎯  
- Jenkins pipeline kullanmasam, aynı işleri yapmak için bash script’leri, batch dosyaları ve manuel komutlar arasında kaybolacağımı, kim hangi script’i nerede çalıştırmış gibi soruların içinden çıkamayacağımı biliyorum; pipeline ise bu süreci tek bir akışta toplayarak hem insan bağımlılığını hem de dokümantasyon açığını kapatıyor. 🧵  
- Pipeline’larımı branch veya environment bazlı parametrik hale getirerek, aynı Jenkinsfile üzerinden hem dev hem test hem de prod ortam deploy’larını yönetebilmek, bana tekrar eden konfigürasyonların önüne geçilen, düzgün soyutlanmış bir CI/CD mimarisi kurma imkanı veriyor. 🧬  

#### Örnek: Dekleratif Jenkinsfile ile Basit CI/CD Akışı 📝

Aşağıdaki Jenkinsfile, tipik bir Java / Maven projemde kullandığım basitleştirilmiş akışı özetliyor; burada amaç, kayıt altında, okunabilir ve her commit sonrası tetiklenebilen bir pipeline tanımı oluşturmak:

```groovy
pipeline {
    agent any

    tools {
        maven 'Maven_3_9'
        jdk   'JDK_17'
    }

    options {
        timestamps()
        skipStagesAfterUnstable()
    }

    triggers {
        pollSCM('H/5 * * * *') // 5 dakikada bir repository değişti mi kontrol et
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }

        stage('Unit Tests') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('Package & Archive') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }

        stage('Docker Build') {
            when {
                branch 'main'
            }
            steps {
                sh '''
                    docker build -t huseyinaydin/my-app:latest .
                    docker push huseyinaydin/my-app:latest
                '''
            }
        }
    }

    post {
        success {
            echo '🎉 Pipeline başarıyla tamamlandı.'
        }
        failure {
            echo '💥 Pipeline patladı, loglara bakma zamanı.'
        }
    }
}
```

Bu pipeline ile; her commit sonrasında kodum derleniyor, testler koşturuluyor, artefact’ler arşivleniyor ve istersem Docker imajı bile otomatik olarak üretilip registry’ye push ediliyor. 🐳  

---

### 4️⃣ Build Agent’lar (Node’lar), Dağıtık Yapı ve Kuyruk Yönetimi 🌐

Build agent mimarisi, Jenkins’i küçük bir oyuncak CI sunucusundan, ciddi anlamda ölçeklenebilir ve dayanıklı bir otomasyon platformuna dönüştüren temel bileşenlerden biri; bu yüzden agent tasarımını yaparken iş yükü, güvenlik ve altyapı limitlerini aynı anda düşünmeye çalışıyorum. 🧠  

- Agent’lar sayesinde, tüm workload’un tek bir makineye binmesi yerine, **farklı iş tipleri için özelleşmiş node’lar** tanımlayabiliyorum; örneğin Docker build’leri için ayrı, Android build’leri için ayrı, .NET veya Java build’leri için ayrı ajanlar kullanarak hem derleme sürelerini kısaltıyor hem de bağımlılık karmaşasını azaltıyorum. 🧩  
- Jenkins’teki “label” mekanizması, belirli job’ların sadece belirli agent’larda çalışmasını sağladığı için, iş yükünü tip bazlı ayırmak istediğimde bana büyük esneklik sağlıyor; örneğin `agent { label 'docker' }` diyerek, Docker’ın yüklü olduğu node’larda koşması gereken job’ları net biçimde yönlendirebiliyorum. 🎯  
- Kuyruk yönetimi (queue), aynı anda birden fazla job tetiklendiğinde, hangi job’ın hangi agent üzerinde ne zaman çalışacağını belirleyen kritik bir mekanizma; yeterli agent sayısı yoksa kuyrukta bekleyen job sayısı artıyor, bu da build sürelerinin toplamını uzatıyor ve geri bildirim döngüsünü bozuyor, bu yüzden kapasite planlamasını takip etmek zorunda olduğumu biliyorum. ⏱️  
- Dağıtık yapı kullanmasam ve her şeyi tek bir Jenkins makinesine yıksam, hem bakım zamanlarında tüm CI/CD trafiğini durdurmak zorunda kalırdım hem de donanımsal bir arıza durumunda bütün pipeline’ların çakılmasına sebep olurdum; agent mimarisi bu riski doğal olarak azaltıyor, çünkü iş yükünü horizontale yaymış oluyorum. 🧱  
- Agent’ları Docker içinde, Kubernetes üzerinde veya klasik bare-metal olarak ayağa kaldırmak tamamen benim tercihime ve ihtiyaçlarıma bağlı; burada asıl önemli nokta, build ortamını “kod” ile tanımlayıp (Dockerfile, Helm chart vs.), tekrar üretilebilir hale getirmek ve Jenkins’i bu ortamlara ince bir orkestratör olarak bağlamak oluyor. 🧬  

#### Örnek: Belirli Label’a Sahip Agent’ta Çalışan Pipeline 👷

```groovy
pipeline {
    agent { label 'docker-node' }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Docker Build & Test') {
            steps {
                sh '''
                    docker build -t my-app:test .
                    docker run --rm my-app:test mvn test
                '''
            }
        }
    }
}
```

Bu örnekte, sadece `docker-node` label’ına sahip agent’ta çalışabilen bir pipeline tanımlıyorum; böylece Docker’ın yüklü olmadığı node’larda yanlışlıkla bu job’ın tetiklenmesini engellemiş oluyorum. 🛡️  

---

### 5️⃣ Plugin Ekosistemi, SCM Entegrasyonları (Git, GitHub, GitLab) ve Bildirimler 🔗

Jenkins’in en sevdiğim taraflarından biri, devasa plugin ekosistemi; çünkü hemen her ihtiyacım için hazır bir yapı taşı bulabiliyor, gerekirse ufak konfigürasyonlarla kendi dünyama uyarlayabiliyorum. 📦  

- SCM entegrasyonları (Git, GitHub, GitLab, Bitbucket) sayesinde, repository’lerimde olan her değişikliği Jenkins ile anında ilişkilendirebiliyor, **webhook** veya **polling** mekanizmalarıyla commit sonrası build tetikleyip, version control dünyası ile CI/CD akışını sıkı sıkıya bağlayabiliyorum. 🔁  
- GitHub veya GitLab ile entegrasyon yapmazsam, commit sonrası build tetiklemek için Jenkins’te manuel job çalıştırmak veya cron’lara güvenmek zorunda kalırdım; oysa webhook’lar sayesinde, repository’ye her push geldiğinde ilgili pipeline kendiliğinden devreye giriyor ve böylece “insan faktörü” sürecin içinden büyük ölçüde çıkıyor. 🤝  
- Plugin ekosisteminde, Docker, Kubernetes, Blue Ocean UI, Email Ext, Slack notifier, JUnit, Allure, SonarQube gibi birçok bileşeni Jenkins’e rahatlıkla entegre edebiliyorum; bu da CI/CD hattımı tek bir boyutta değil, hem test, hem kalite, hem deploy, hem izleme tarafında güçlendirmemi sağlıyor. 🌈  
- Bildirim tarafında, Slack, e-posta veya diğer chat ops araçlarına entegre ettiğim pipeline sonuçları sayesinde, işin sadece Jenkins UI’da kalmamasını ve ekibin günlük iletişim kanalları üzerinden build sonuçlarından haberdar olmasını sağlayabiliyorum; bu da ekip içi görünürlüğü ve sorumluluk paylaşımını ciddi anlamda iyileştiriyor. 📨  
- Plugin kullanırken, aşırıya kaçmanın da riskli olduğunun farkındayım; çok fazla plugin yüklemek, hem bakım zorluğu hem de güvenlik açıkları açısından beni zor durumda bırakabilir, bu nedenle gerçekten ihtiyacım olmayan plugin’leri sistemde tutmamaya, düzenli olarak güncelleme ve temizlik yapmaya özen gösteriyorum. 🧹  

#### Örnek: GitHub Entegrasyonlu Basit Pipeline ve Slack Bildirimi 💬

```groovy
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/huseyinaydin99/jenkins-ci-cd-demo.git'
            }
        }

        stage('Build & Test') {
            steps {
                sh 'mvn -B clean verify'
            }
        }
    }

    post {
        success {
            slackSend(
                channel: '#ci-cd',
                message: "✅ Build başarılı oldu: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                color: 'good'
            )
        }
        failure {
            slackSend(
                channel: '#ci-cd',
                message: "❌ Build HATALI: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                color: 'danger'
            )
        }
    }
}
```

Bu örnekte, GitHub’daki bir repository’den kod çekip Maven ile build alırken, sonuçları Slack kanalına gönderen uçtan uca küçük ama işlevsel bir akış tanımlıyorum. 📣  

---

### 6️⃣ Güvenlik, Yetkilendirme, Backup/Restore ve CI/CD En İyi Pratikleri 🛡️

Jenkins tarafında yıllar içinde öğrendiğim en önemli şeylerden biri, CI/CD’nin sadece build ve deploy hızından ibaret olmadığı; güvenlik, yetkilendirme, yedekleme ve genel best practice’lerin de bu oyunun vazgeçilmez parçaları olduğu gerçeği. 🔐  

- Güvenlik tarafında, **anonymous access’i** minimumda tutmak, kullanıcıları LDAP / AD veya GitHub / GitLab hesapları üzerinden tanımlamak ve role-based access control ile kimin hangi job’ı çalıştırabileceğini net çizgilerle belirlemek, hem audit açısından hem de “yanlışlıkla yapılan prod deploy’larını” engellemek açısından hayati. 🧱  
- Jenkins’i güncel tutmamak, plugin’leri rastgele güncellemek veya hiç güncellememek, zamanla ciddi güvenlik açıklarına yol açabiliyor; bu nedenle, güncelleme notlarını okuyarak, test ortamında deneme yaptıktan sonra prod Jenkins’i upgrade etmek, benim için olmazsa olmaz bir disiplin haline geldi. 📅  
- Backup/restore tarafında, `JENKINS_HOME` dizinini düzenli aralıklarla yedeklemek, job konfigürasyonları, build geçmişleri, pipeline tanımları ve credential kayıtlarının olası bir felakette kaybolmamasını sağlıyor; aksi halde, tek bir disk hatası tüm CI/CD kurgumu sıfırlayabilir, bu da hem zaman hem de motivasyon açısından büyük bir yıkım olur. 💾  
- En iyi pratikler tarafında, pipeline’ları **idempotent** ve tekrar çalıştırılabilir olacak şekilde tasarlamaya özen gösteriyorum; yani aynı pipeline’ı birkaç kez çalıştırdığımda, sistemde yan etkiler bırakmayan, environment bağımlılıklarını minimize eden bir akış hedefliyorum, böylece hata durumunda yeniden deneme yapmak korkutucu olmaktan çıkıyor. 🔁  
- CI/CD’de temel amacın, “her commit’in potansiyel olarak prod’a gidebilecek kadar kaliteli ve güvenli olmasını sağlamak” olduğunun farkında olarak, Jenkins’i sadece otomasyon değil, aynı zamanda **kalite ve güvenlik barajı** olarak konumlandırıyorum; testler, code coverage, static analysis, security scan gibi adımları pipeline’a yerleştirmek, uzun vadede borçları ertelemek yerine, erken aşamada yakalamamı sağlıyor. 🧮  

#### Örnek: Basit Backup Script’i ile JENKINS_HOME Yedekleme 💾

Aşağıda gösterdiğim örnek, cron ile zamanlanabilecek basit bir backup script’i; bu sayede Jenkins konfigürasyonlarımı düzenli olarak arşivleyebiliyorum:

```bash
#!/usr/bin/env bash
set -e

JENKINS_HOME="/var/jenkins_home"
BACKUP_DIR="/backups/jenkins"
TIMESTAMP=$(date +"%Y%m%d-%H%M%S")

mkdir -p "${BACKUP_DIR}"

tar -czf "${BACKUP_DIR}/jenkins-home-${TIMESTAMP}.tar.gz" -C "${JENKINS_HOME}" .

echo "✅ Jenkins backup tamamlandı: ${BACKUP_DIR}/jenkins-home-${TIMESTAMP}.tar.gz"
```

Bu basit yaklaşım bile, beklenmedik bir sunucu arızasında veya migration sırasında, Jenkins’i sıfırdan kurmak yerine, yedeğimden geri dönebilmemi sağlıyor. 🛟  

---

### 🎯 Jenkins + CI/CD: İkisi Bir Arada Nasıl Bir Değer Üretiyor?

Toparlayacak olursam, Jenkins tek başına sadece bir araç, CI/CD ise tek başına soyut bir prensip; ama ikisini bir araya getirdiğimde, **yazdığım kodun commit’ten üretime kadar izlenebilir, ölçülebilir ve otomatize bir yolculuğa dönüştüğünü** net biçimde hissediyorum.  

- Her push sonrası otomatik çalışan pipeline’lar, bana sürekli geri bildirim sağlayan, hataları prod’a gitmeden önce yakalayan ve ekibin tamamını daha disiplinli kod yazmaya teşvik eden görünmez bir rehber gibi davranıyor; bu sayede yazılım geliştirme sürecim, sadece “kod yazdım bitti” noktasında kalmıyor, “kodumu üretime kadar taşıyan olgun bir yaşam döngüsü” haline geliyor. 🌍  
- Jenkins ile kurduğum CI/CD yapısı, hem bireysel olarak beni daha üretken, daha özgüvenli ve daha hatasız kılarken, ekip ölçeğinde de iş birliğini ve teslim hızını artıran stratejik bir yatırım olarak geri dönüyor; sonuçta bu sayede hem işveren tarafı hem de geliştirici olarak ben, daha az sürpriz ve daha çok öngörülebilirlikten faydalanıyorum. 🚀  


![jenkis](https://github.com/user-attachments/assets/8bfbd3cb-5600-4a88-bec2-d3fe81ab0f1c)
