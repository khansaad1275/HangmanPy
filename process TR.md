# HangmanPy Process TR

## Fikir
Python öğrenmeye karar verdikten sonra bir süre en iyi kaynaklar nelerdir diye internette araştırma yaptım ve Coursera'da tam istediğim gibi 5 kursluk bir seri buldum. Ancak programlamada ne kadar iyi bir eğitim ya da kurs söz konusu olursa olsun pratik yapmadan ilerlemenin mümkün olmadığını biliyordum bu yüzden de aklımın bir köşesinde hep kendimi geliştirme sürecinde yapabileceğim proje bulmak vardı. Fikir çoktu ama o kadar az bilgim vardı ki bu fikirleri hayata geçirmek için henüz hazır değildim. Ancak ilk kursu bitirirken yani yaklaşık iki hafta çalıştıktan sonra fark ettim ki hiçbir zaman hazır olmayacağım. Kendi projelerimi geliştirmeden de öğrendiklerimi pekiştirmem mümkün değildi. Bu yüzden Python çalışırken aklıma gelen adam asmaca oyununu projelendirmeye karar verdim. Bu bölümde de ilerlediğim aşamalar boyunca size projenin aşamalarından ve nasıl gerçekleştiğinden bahsedeceğim.


## Yöntem
Normal koşullarda bir proje yaparken sonuca ulaşma sürecini olabildiğince kısa tutmaya çalışmak mantıklıdır. Bunun için de daha önce yapılmış benzer projelere bakmak, kodlarını incelemek, forklamak gerekir. Benim içinse durum farklı oldu çünkü amacım projeyi hayata geçirmek değil, biraz debelenerek zorlanarak da olsa Python'u daha detaylı öğrenebilmek. O yüzden bir iskelet ya da hazır materyaller üzerine çalışmak yerine her bir aşamasını kendi başıma yapmaya çalışarak, araştırarak, deneyerek, öğrenerek ilerlemeye karar verdim. Mutlaka aralarda desteğe ihtiyacım olacak ve internet üzerinden cevaplar arayacağım ama bu zorlanmanın beni daha iyi bir programcı yapacağına inanıyorum.


## Aşama 1 : Soru kelimesi belirleme
Adam asmaca bir kelime oyunu olduğu için başlangıç noktamın bu kelimeyi belirleme olması gerekiyordu. Bunun için yapabileceğim bir iki seçenek vardı. Bir tanesi bir excel ya da text dosyası hazırlayıp kendi belirlediğim kelimelerden birini programın seçmesini sağlamak; diğeri ise işe biraz daha ciddi yaklaşıp online bir sözlükten veri çekmekti. Ben ikincisini tercih ettim çünkü kendi belirlediğim sınırlı sayıda kelimeyle oyunun tadı çıkmayacaktı. Üzerinde epey bir çalışacağım düşünülürse çalışırken öncelikle benim için de eğlenceli ve sürprizli olması; sonrasında ise kullanacak olanlar için tahmin edilemez olmasını istedim. Madem her şeyi sıfırdan yapıyorum neden biraz kendimi zorlamayayım değil mi?

<img src="http://i65.tinypic.com/205r410.png" alt="Oxford Dictionary API for developers" width="700"/>

Bu kararımla birlikte hemen online sözlük apilerini araştırmaya başladım ve Oxford'un böyle bir imkanı olduğunu gördüm. [Oxford Developer Tool](https://developer.oxforddictionaries.com/) kısmından sınırlı kullanıma uygun bir ücretsiz hesap ve API oluşturdum ve Oxford bana aşağıdaki gibi API bilgilerimi verdi.

<img src="http://i68.tinypic.com/34yvjbt.png" alt="Oxford Dictionary API info" width="400"/>

Uygulamanın çok iyi bir dökümantasyonu olduğunu görünce çok mutlu oldum çünkü benim gibi bir çaylak için bu, bulunmaz bir nimetti. Python için aşağıda da görebileceğiniz API kodunu denedim hemen.

```Python
# for more information on how to install requests
# http://docs.python-requests.org/en/master/user/install/#install
import  requests
import json
# TODO: replace with your own app_id and app_key
app_id = '<my app_id>'
app_key = '<my app_key>'
language = 'en'
word_id = 'Ace'
url = 'https://od-api.oxforddictionaries.com:443/api/v1/entries/'  + language + '/'  + word_id.lower()
#url Normalized frequency
urlFR = 'https://od-api.oxforddictionaries.com:443/api/v1/stats/frequency/word/'  + language + '/?corpus=nmc&lemma=' + word_id.lower()
r = requests.get(url, headers = {'app_id' : app_id, 'app_key' : app_key})
print("code {}\n".format(r.status_code))
print("text \n" + r.text)
print("json \n" + json.dumps(r.json()))
```

API id ve key bilgilerini girdikten sonra Terminal'de çalıştırınca önümde yüzlerce satırlık json dosyası akmaya başladı. Üstelik bu sadece test kodu içerisinde verilmiş `word_id` olarak belirlenmiş olan **Ace** kelimesi içindi. Kodun son kısmındaki `print` komutlarını tek tek silerek neyin ne olduğunu anlamaya çalıştım. Bu yöntem çok işe yaramayınca json dosyasını online bir görüntüleyicide açarak verileri kontrol ettim. En kaba haliyle uygulamanın bana verdiği temel bilgiler aşağıdaki gibiydi ve detayları sanki sonsuza uzanıyordu.

<img src="http://i68.tinypic.com/qzir9i.png" alt="Oxford Dictionary API json test" width="300"/>

Bu noktada önümde çözmem gereken iki durum vardı. Birincisi bu json verileri arasından benim işime yarayacak bir sadeleştirme geliştirebilmek, diğeri de rastgele bir kelime atanmasını sağlamak. Çünkü uygulama temel olarak bir kelime girilmesi ve onunle ilgili verilerin çekilmesi üzerine çalışıyor ama benim ihtiyacım olan bu değildi. Ben API üzerinden kelime sorgulamayı değil, rastgele bir kelime seçilmesini istiyordum.
