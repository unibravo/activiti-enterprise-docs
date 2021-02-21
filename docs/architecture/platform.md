---
Başlık: Platform hizmetleri
---

# Platform hizmetleri
Platform hizmetleri, tek bir Kubernetes ad alanına dağıtılan hizmetlerdir ve Activiti Enterprise tarafından, uygulama ve hizmetlerin kaç bireysel uygulamanın dağıtıldığına bakılmaksızın tüm dağıtım boyunca yönetilmesi için kullanılır.

## Modelleme hizmeti
Modelleme hizmeti, aşağıdakiler için gerekli arka uç işlevselliğini içerir: [Alfresco Modelleme Uygulaması](../modeling/README.md) çalıştırmak için. [deployment service](#deployment-hizmeti) in projeleri dağıtmak için kullandığı proje ve model tanımlarını depolamak için kendisiyle birlikte dağıtılan bir Postgres örneğini gerektirir. Bu Postgres örneği, bir platform düzeyinde konuşlandırılır ve [application services](../architecture/application.md) tarafından kullanılandan bağımsızdır. Ayrıca, dağıtım hizmeti tarafından kullanılan Postgres örneğinden bağımsızdır.

Modelleme hizmeti ayrıca simülasyon hizmetlerini de içerir, böylece karar tablosu ve komut dosyası işlevselliği modelleme deneyimi sırasında test edilebilir.

Modelleme hizmetinin sunduğu REST API'leri projeler, modeller ve sürümlerle ilgilenir.

Aşağıda modelleme hizmetinin yüksek seviyeli bir diyagramı verilmiştir:

[Modelleme hizmeti diyagramı](../images/arch-modeling.png)

## Deployment hizmeti
Dağıtım hizmeti, dağıtım tanımlayıcıları oluşturmak ve yayımlanan projeleri dağıtmak için kullanılır.

* Bir dağıtım tanımlayıcısı oluşturmak için dağıtım hizmetini kullanmak, bir Helm grafiği olarak indirilebilen ve daha sonra Helm aracılığıyla dağıtılabilen yayınlanmış bir proje için bir tanımlayıcı oluşturmak üzere API'yi kullanır. Activiti Enterprise kümesine bir dağıtım tanımlayıcısı da yerleştirilebilir.

* Yayınlanmış bir projeyi dağıtmak için dağıtım hizmetini kullanmak bir dağıtım tanımlayıcısı oluşturur, ancak uygulamayı kendi ad alanındaki Activiti Enterprise kümesine dağıtır.

Aşağıdaki diyagram, dağıtım hizmetinin bir dağıtım tanımlayıcısı oluşturmak ve yayımlanmış bir projeyi dağıtmak için uyguladığı adımları gösterir:

![Deployment service diagram](../images/arch-deployment-service.png)

API aracılığıyla veya Yönetici Uygulaması kullanılarak dağıtım hizmetine bir yük gönderildiğinde, bir dizi olay gerçekleşir:

* İlk şey, yükün hata içermediğinden ve kümede halihazırda konuşlandırılmış diğer uygulama adlarıyla hiçbir çakışma olmadığından emin olmak için doğrulamadır.
* Doğrulama geçtikten sonra, yüke varsayılan değerleri belirten bir dizi veri zenginleştirme uygulanır.
* Veri zenginleştirme tamamlandıktan sonra yük, açıklayıcı olarak dağıtım hizmeti veritabanına kaydedilir. Varsayılan olarak bu, adı verilen bir PostgreSQL veritabanı örneğidir **aps2-postgresql-ads**.
* Temel görüntüler Docker kayıt defterinde zaten var [application services](../architecture/application.md) ve dağıtım zamanında yeniden kullanılır. Çalışma zamanı paketi, form hizmeti ve DMN hizmeti için, statik bilgileri içeren kalıcı bir birime başvuran bir temel görüntü kullanılır. [project definition files](../modeling/projects.md#files). Kalıcı birim oluşturulur ve yeni namespace'e eklenir

	**Not**: Bu noktada bir dağıtım tanımlayıcısı indirilebilir.

* Dağıtmanın son aşaması, görüntüleri kendi ad alanlarına dağıtmak için Kubernetes API'yi kullanır. Bu aynı zamanda yeni ad alanına takılan kalıcı bir birimi de içerir.

	**Not**: Dağıtım hizmetini kullanarak bir dağıtım tanımlayıcısını dağıtmak, indirmek ve Helm aracılığıyla manuel olarak dağıtmak hala mümkündür.

## Alfresco Content Services

Bir içerik havuzunda devam eden görevler ve işlemler hakkındaki bilgileri depolamak için Alfresco Activiti Enterprise'ın bir parçası olarak bir örnek yerleştirilir [Alfresco Content Services (ACS)](https://docs.alfresco.com/6.1/references/whats-new.html). Activiti Enterprise'ı kullanmak için ACS gerekli değildir ve görevler ve süreçler için veriler yine de veritabanlarında saklanır.

## Kimlik Hizmeti
Alfresco Activiti Enterprise, ürün genelinde kimlik doğrulama ve kullanıcı ve rol yönetimi için Kimlik Hizmetini kullanır [Identity Service](https://docs.alfresco.com/identity/concepts/identity-overview.html). 
Kimlik doğrulama, LDAP ve SAML gibi harici kimlik sağlayıcı örneklerine yapılandırılabilir: [configured](http://docs.alfresco.com/identity/concepts/identity-configure.html) 

Alfresco Yönetici Uygulaması, yöneticilerin Kimlik Hizmetine erişmeye gerek kalmadan kullanıcıyla ilgili genel işlevleri yönetmelerine olanak tanır. [Alfresco Administrator Application](../administrator/identity/README.md)
