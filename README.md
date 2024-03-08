Bu repoda TS500 ve TBDY2018 deki konuların python yardımı ile kodlaması yapılarak hesaplanması amaçlanmaktadır.

# Yapılan ve yapılması hedeflenen konu başlıkları
- [x] Dikdörtgen kolonlarda sargı donatısı tasarımı.
- [x] TBDY-2018 deprem yönetmeliğinde EK5-A da belirtilen sargılı ve sargısız mander beton modeli ile çelik modelinin oluşturulması.
- [x] TBDY2018 bölüm 3'te verilen spektrum grafiklerinin çıkarılması.
- [x] Verilen bilgilere göre BYS sınıfı ve yapılabilecek maximum bina yüksekliğinin bulunması.
- [ ] Etabs programı ile bağlantı ve sonuçların alınması
- [ ] Göreli kat öteleme kontrollerinin yapılması
- [ ] Deprem kaydı seçimi, ivme kaydı okunması, spektral ivme,hız ve deplasman serilerinin çıkarılması ve ölçekleme işlemlerinin yapılması.
- [ ] Lifli polimer ile sargılanan kolonlarda dayanım ve süneklilik artışının hesabı 
- [ ] Verilen bilgilere göre performans hedeflerinin bulunması.
- [ ] TBDY2018'e uygun R ve D katsayıları önermesi.
- [ ] TBDY2018'e göre Eşdeğer deprem yüklerinin bulunması.
- [ ] TBDY2018'e göre konsol istinatlarda kuvvetlerin bulunması.
- [ ] TS500'e göre guse hesabı.


# Repo ile ilgili özet bilgiler
<p align="center">
  <a href="https://github.com/muhammedsural/TSC2018_Design/graphs/contributors"><img src="https://img.shields.io/github/contributors/muhammedsural/TSC2018_Design?style=for-the-badge"></a>
  <a href="https://github.com/muhammedsural/TSC2018_Design/network/members"><img src="https://img.shields.io/github/forks/muhammedsural/TSC2018_Design?style=for-the-badge"></a>
  <a href="https://github.com/muhammedsural/TSC2018_Design/stargazers"><img src="https://img.shields.io/github/stars/muhammedsural/TSC2018_Design?style=for-the-badge"></a>
  <a href="https://github.com/muhammedsural/TSC2018_Design/issues"><img src="https://img.shields.io/github/issues/muhammedsural/TSC2018_Design?style=for-the-badge"></a>
  <a href="https://github.com/muhammedsural/TSC2018_Design/blob/master/LICENSE"><img src="https://img.shields.io/github/license/muhammedsural/TSC2018_Design?style=for-the-badge"></a>
</p><br />

# 💬 Contact

<a href="https://twitter.com/SuralMuhammet" target="_blank">
  <img src=https://img.shields.io/twitter/url?label=Twitter&logo=Twitter&style=for-the-badge&url=https%3A%2F%2Ftwitter.com%2FSuralMuhammet alt=twitter style="margin-bottom: 5px;" </img>
</a>

<a href="https://www.linkedin.com/in/muhammedsural/" target="_blank">
<img src=https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white alt=linkedin style="margin-bottom: 5px;" />
</a>

<a href="mailto:muhammedsural@gmail.com" target="_blank">
<img src=https://img.shields.io/badge/Gmail-D14836?style=for-the-badge&logo=gmail&logoColor=white alt=gmail style="margin-bottom: 5px;" />
</a> 

# Örnek Çalışma

## 1- Modüllerin import edilmesi

```python
from TSCMaterialModels import Mander
from TSCConfimentBarsRules import ConfimentDesign as cd
from Definitions import DuctilityLevel, ResSystemType, SlabSystem,SeismicResistanceBuildingsClass
from TSCResponseSpectra import *
```

## 2- Kullanılacak değerler

```python
"""Units N,mm"""
Nd                      = 16000 
B                       = 400
H                       = 400
s                       = 80
TieRebarDiameter        = 8
LongnitRebarDiameter    = 14
ClearCoverConc          = 25
NumBarsTop              = 2
NumBarsInterior         = 1
NumBarsBot              = 2
X_tiebars               = 2
Y_tiebars               = 3
fsy                     = 220
fywe                    = 220
eps_su                  = 0.08
f_co                    = 25
f_ce                    = 25
Fctd                    = 10
Ln                      = 2600
```

## 3- TBDY2018 dikdörtgen kolon sargı donatısı tasarımı

```python
ConfinmentDesign = cd(Nd, fsy, Fctd, Ln, B, H, ClearCoverConc, X_tiebars, Y_tiebars, f_co, fywe, TieRebarDiameter, LongnitRebarDiameter)
```
Kolon Serbest Bölgesindeki Etriye Adeti - Etriye Çapi / SarılmaDışıAralık / OrtaSarılmadakiAralık / UçSarılmaAralık = 42 - ∅8 / 16 / 5 / 5

```python
s = ConfinmentDesign.s_OptEndConfArea
```
52

## 4- TBDY2018 malzeme modelleri
![Mander](src/Resource/TBDY_Mander_part.png)
```python
mander = Mander(B                    = B,
                H                    = H,
                s                    = s,
                TieRebarDiameter     = TieRebarDiameter,
                LongnitRebarDiameter = LongnitRebarDiameter,
                ClearCoverConc       = ClearCoverConc,
                NumBarsTop           = NumBarsTop,
                NumBarsInterior      = NumBarsInterior,
                NumBarsBot           = NumBarsBot,
                X_tiebars            = X_tiebars,
                Y_tiebars            = Y_tiebars,
                fsy                  = fsy,
                f_ywe                = fywe,
                eps_su               = eps_su,
                f_co                 = f_co,
                f_ce                 = f_ce
                )

mander.Plot_Manders()
```
![ManderPlot](src/Resource/ExampleManderPlot.png)

## 5-TBDY2018 Hedef Spektrumların Elde Edilmesi
TBDY2018 de verilen spektrumları elde etmek için sismik girdiler için oluşturulan `SeismicInputs` sınıfından faydalanıyoruz. Sismik verilerin girişi için `SeismicInputs` örnek sınıfımızın instance'nı alıyoruz. Bu sınıf diğer sınıflarda girdi olarak kullanılacak.
```python
SeismicVariables = SeismicInputs(lat = 39.85,lon = 30.2,soil = "ZC",intensity = "DD2")
SeismicVariables
```
<p>Latitude :39.85</p>
<p>Longitude :30.2</p>
<p>Soil Class :ZC</p>
<p>Intensity:DD2</p>

Bina modeli ile ilgili bilgileri `SeismicResistanceBuildingInputs` sınıfında veriyoruz. Burada sınıflandırmalar için `Enum` sınıfları olan `DuctilityLevel`,`ResSystemType`,`SlabSystem` kullandık.

```python
RCBuilding = SeismicResistanceBuildingInputs(Hn=70,
                                                 I=1,
                                                 DuctilLevel=DuctilityLevel.Yuksek,
                                                 ResSystemType_X=ResSystemType.BAKarma,
                                                 ResSystemType_Y=ResSystemType.BAKarma,
                                                 SlabSystem=SlabSystem.Plak_kirisli)
RCBuilding
```

<p>Hn :70</p>
<p>I :1</p>
<p>DuctilLevel :Yuksek</p>
<p>ResSystemType_X :BAKarma</p>
<p>ResSystemType_Y :BAKarma</p>
<p>SlabSystem :Plak_kirisli</p>

Spektrum değerlerinin bulunması için `SeismicInputsManager` sınıfı kullanılıyor. Bu sınıf `SeismicVariables` sınıfının bilgilerini girdi olarak kullanır ve diğer değerleri `SetVariables` fonksiyonu çalıştırılırsa hesaplayıp sınıf property lerine set eder.

```python
SIM = SeismicInputsManager(SeismicVariables=SeismicVariables, TL=6.0)
SIM.SetVariables()
SIM
```

Ss :0.737
S1 :0.195
PGA :0.309
PGV :18.833
Fs :1.205
F1 :1.5
SDs :0.888085
SD1 :0.2925
TA :0.06587207305607008
TB :0.3293603652803504
TL :6.0

`SeismicResistanceBuildingManeger` sınıfı bina bilgilerini içeren `SeismicResistanceBuildingInputs` ve sismik verilerin hesabını yapan `SeismicInputsManager` sınıflarını girdi olarak alır ve genel bina sınıflandırma işlemlerini `SetVariables` fonksiyonu çalıştırılarak hesaplar ve propertylere set eder.

```python
Srbm = SeismicResistanceBuildingManeger(BuildingVariables=RCBuilding, SeismicManager=SIM, BuildingClass=SeismicResistanceBuildingsClass.A14, Rx=6,Ry=3)
Srbm.SetVariables()
Srbm
```

SeismicResistanceBuildingManeger(BuildingVariables=Hn :70
I :1
DuctilLevel :Yuksek
ResSystemType_X :BAKarma
ResSystemType_Y :BAKarma
SlabSystem :Plak_kirisli, SeismicManager=Ss :0.737
S1 :0.195
PGA :0.309
PGV :18.833
Fs :1.205
F1 :1.5
SDs :0.888085
SD1 :0.2925
TA :0.06587207305607008
TB :0.3293603652803504
TL :6.0, BuildingClass=<SeismicResistanceBuildingsClass.A14: 5>, Total_M_DEV=0, Total_M_o=0, DTS=2, BYS=2, Rx=6, Ry=3, Dx=1.0, Dy=1.0)

`Spectrum` sınıfı `SeismicResistanceBuildingManeger` sınıfı kullanarak yapıya ait spektrum bilgilerini `SetVariables` fonksiyonu çalıştırılarak hesaplayıp `ElasticSpectrums` değişkenine set eder. Bu property `pandas.DataFrame` döndürür.

```python
Spec = Spectrum(BuildingManager=Srbm)
Spec.SetVariables()
Spec
```

Spectrum(BuildingManager=SeismicResistanceBuildingManeger(BuildingVariables=Hn :70
I :1
DuctilLevel :Yuksek
ResSystemType_X :BAKarma
ResSystemType_Y :BAKarma
SlabSystem :Plak_kirisli, SeismicManager=Ss :0.737
S1 :0.195
PGA :0.309
PGV :18.833
Fs :1.205
F1 :1.5
SDs :0.888085
SD1 :0.2925
TA :0.06587207305607008
TB :0.3293603652803504
TL :6.0, BuildingClass=<SeismicResistanceBuildingsClass.A14: 5>, Total_M_DEV=0, Total_M_o=0, DTS=2, BYS=2, Rx=6, Ry=3, Dx=1.0, Dy=1.0))

```python
Spec.ElasticSpectrums
Spec
```

![image](src/Resource/df_Spectrums.png)

Bütün grafiklerin tek seferde gösterimi için `plot_Spectrums` fonksiyonu çalıştırılabilir. Özel olarak hazırlanmış formatta grafikler tek parçada çizdirilir.

```python
Spec.plot_Spectrums()
```

![image](src/Resource/AllSpectrums_And_Ra.png)

İlgili sınıflardaki fonksiyonlar tek tekte kullanılabilir. Örneğin belirli bir periyot için elastik ve azaltılmış elastik spektrum değerleri aşağıdaki fonksiyonlar yardımıyla elde edilebilir.

```python
Sae_Tp = Spec.Get_Sae_Tp(T=1.2,
                         TA = Spec.BuildingManager.SeismicManager.TA,
                         TB = Spec.BuildingManager.SeismicManager.TB,
                         SDs= Spec.BuildingManager.SeismicManager.SDs,
                         SD1= Spec.BuildingManager.SeismicManager.SD1,
                         TL = Spec.BuildingManager.SeismicManager.TL 
                         )
Sae_Tp
```

0.2438

```python
Sar_Tp = Spec.Get_SaR_Tp(R  = Spec.BuildingManager.Rx,
                         D  = Spec.BuildingManager.Dx,
                         T  = 1.2,
                         TB = Spec.BuildingManager.SeismicManager.TB,
                         I  = Spec.BuildingManager.BuildingVariables.I,
                         TA = Spec.BuildingManager.SeismicManager.TA,
                         SDs= Spec.BuildingManager.SeismicManager.SDs,
                         SD1= Spec.BuildingManager.SeismicManager.SD1,
                         TL = Spec.BuildingManager.SeismicManager.TL  )
Sar_Tp
```

0.0406

