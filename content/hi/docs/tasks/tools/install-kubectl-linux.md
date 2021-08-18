---

title: लिनक्स पर kubectl इंस्टॉल और सेट करें
content_type: कार्य
weight: 10
card:
  name: tasks
  weight: 20
  title: लिनक्स पर kubectl इंस्टॉल करें
---

## {{% heading "पूर्वाकांक्षित" %}}

आपको kubectl संस्करण का उपयोग करना चाहिए जो आपके क्लस्टर के एक मामूली संस्करण अंतर के भीतर हो. उदाहरण के लिए,  v{{< skew latestVersion >}} क्लाइंट निम्नलिखित के साथ संवाद कर सकते हैं v{{< skew prevMinorVersion >}}, v{{< skew latestVersion >}}, and v{{< skew nextMinorVersion >}} कण्ट्रोल प्लेन.
kubectl के नवीनतम संस्करण का उपयोग करने से अप्रत्याशित मुद्दों से बचने में मदद मिलती है.

## लिनक्स पर kubectl स्थापित करें

लिनक्स पर kubectl संस्थापित करने के लिए निम्नलिखित विधियाँ मौजूद हैं::

- [लिनक्स पर curl के माध्यम से kubectl बाइनरी इंस्टॉल करें](#लिनक्स-पर-curl-के-माध्यम-से-kubectl-बाइनरी-इंस्टॉल-करें)
- [नेटिव पैकेज मैनेजमेंट का उपयोग करते हुए इंस्टॉल करे](#नेटिव-पैकेज-मैनेजमेंट-का-उपयोग-करते-हुए-इंस्टॉल-करे)
- [अन्य पैकेज प्रबंधन का उपयोग करके इंस्टॉल करें](#अन्य-पैकेज-प्रबंधन-का-उपयोग-करके-इंस्टॉल-करें)

### लिनक्स पर curl के माध्यम से kubectl बाइनरी स्थापित करें

1. कमांड के साथ नवीनतम रिलीज डाउनलोड करें:

   ```bash
   curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
   ```

   {{< note >}}
एक विशिष्ट संस्करण डाउनलोड करने के लिए, यह `$(curl -L -s https://dl.k8s.io/release/stable.txt)` कमांड के हिस्से को विशिष्ट संस्करण से बदलें.

उदाहरण के लिए, लिनक्स पर यह {{< param "fullversion" >}} संस्करण डाउनलोड करने के लिए, टाइप करें :

   ```bash
   curl -LO https://dl.k8s.io/release/{{< param "fullversion" >}}/bin/linux/amd64/kubectl
   ```
   {{< /note >}}

1. बाइनरी को मान्य करें (ऐच्छिक)

  kubectl चेकसम फाइल डाउनलोड करें:

   ```bash
   curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
   ```

   चेकसम फ़ाइल के विरुद्ध kubectl बाइनरी को मान्य करें :

   ```bash
   echo "$(<kubectl.sha256) kubectl" | sha256sum --check
   ```

   यदि मान्य है, तो आउटपुट है:

   ```console
   kubectl: OK
   ```

   अगर चेक फेल हो जाता है, `sha256` गैर-शून्य स्थिति के साथ बाहर निकलता है और इस आउटपुट के समान प्रिंट करता है:

   ```bash
   kubectl: FAILED
   sha256sum: WARNING: 1 computed checksum did NOT match
   ```

   {{< note >}}
   बाइनरी और चेकसम का एक ही संस्करण डाउनलोड करें.
   {{< /note >}}

1. kubectl स्थापित करें

   ```bash
   sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
   ```

   {{< note >}}
   यदि आपके पास टारगेट सिस्टम पर रुट एक्सेस नहीं है, आप अभी भी kubectl को `~/.local/bin` डायरेक्टरी में इंस्टॉल कर सकते हैं:

   ```bash
   chmod +x kubectl
   mkdir -p ~/.local/bin/kubectl
   mv ./kubectl ~/.local/bin/kubectl
   # and then add ~/.local/bin/kubectl to $PATH
   ```

   {{< /note >}}

1. यह सुनिश्चित करने के लिए परीक्षण करें कि आपके द्वारा इंस्टॉल किया गया संस्करण अद्यतित है:

   ```bash
   kubectl version --client
   ```

### नेटिव पैकेज मैनेजमेंट का उपयोग करते हुए स्थापित करे

{{< tabs name="kubectl_install" >}}
{{% tab name="Debian-based distributions" %}}

1. `apt` पैकेज इंडेक्स को अपडेट करे और कुबेरनेट्स `apt` रिपॉजिटरी का उपयोग करने के लिए आवश्यक पैकेज इंस्टॉल करें:

   ```shell
   sudo apt-get update
   sudo apt-get install -y apt-transport-https ca-certificates curl
   ```

2. गूगल क्लाउड पब्लिक साइनिंग कुंजी (key) डाउनलोड करें:

   ```shell
   sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
   ```

3. कुबेरनेट्स `apt` रिपॉजिटरी को जोड़े:

   ```shell
   echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
   ```

4. नए रिपॉजिटरी के साथ `apt` पैकेज इंडेक्स अपडेट करें और kubectl इंस्टॉल करें:

   ```shell
   sudo apt-get update
   sudo apt-get install -y kubectl
   ```

{{% /tab %}}

{{< tab name="Red Hat-based distributions" codelang="bash" >}}
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF
yum install -y kubectl
{{< /tab >}}
{{< /tabs >}}

### अन्य पैकेज प्रबंधन का उपयोग करके इंस्टॉल करें

{{< tabs name="other_kubectl_install" >}}
{{% tab name="Snap" %}}
यदि आप उबंटू या किसी अन्य लिनक्स वितरण पर हैं जो [snap](https://snapcraft.io/docs/core/install) पैकेज मैनेजर को सपोर्ट करता है, kubectl [snap](https://snapcraft.io/) एप्लिकेशन के रूप में उपलब्ध है.

```shell
snap install kubectl --classic
kubectl version --client
```

{{% /tab %}}

{{% tab name="Homebrew" %}}
यदि आप Linux पर हैं और [Homebrew](https://docs.brew.sh/Homebrew-on-Linux) पैकेज मैनेजर का उपयोग कर रहे हैं, kubectl [इंस्टालेशन](https://docs.brew.sh/Homebrew-on-Linux#install) के लिए उपलब्ध है.

```shell
brew install kubectl
kubectl version --client
```

{{% /tab %}}

{{< /tabs >}}

## Kubectl कॉन्फ़िगरेशन सत्यापित करें

{{< include "included/verify-kubectl.md" >}}

## वैकल्पिक Kubectl कॉन्फ़िगरेशन और प्लगइन्स

### शेल ऑटोकम्प्लेशन सक्षम करें

kubectl Bash और Zsh के लिए ऑटोकम्प्लेशन का सपोर्ट प्रदान करता है, जो आपको बहुत सारी टाइपिंग बचा सकता है.

नीचे Bash और Zsh के लिए ऑटोकम्प्लेशन स्थापित करने की प्रक्रियाएँ हैं.

{{< tabs name="kubectl_autocompletion" >}}
{{< tab name="Bash" include="included/optional-kubectl-configs-bash-linux.md" />}}
{{< tab name="Zsh" include="included/optional-kubectl-configs-zsh.md" />}}
{{< /tabs >}}

### इंस्टॉल `kubectl convert` प्लगइन

{{< include "included/kubectl-convert-overview.md" >}}

1. कमांड के साथ नवीनतम रिलीज डाउनलोड करें:

   ```bash
   curl -LO https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl-convert
   ```

1. बाइनरी को मान्य करें (ऐच्छिक)

   kubectl-convert चेकसम फ़ाइल डाउनलोड करें:

   ```bash
   curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl-convert.sha256"
   ```

   चेकसम फ़ाइल के विरुद्ध kubectl-convert बाइनरी को मान्य करें:

   ```bash
   echo "$(<kubectl-convert.sha256) kubectl-convert" | sha256sum --check
   ```

   यदि मान्य है, तो आउटपुट है:

   ```console
   kubectl-convert: OK
   ```

   अगर चेक फेल हो जाता है, `sha256` गैर-शून्य स्थिति के साथ बाहर निकलता है और इस आउटपुट के समान प्रिंट करता है:

   ```bash
   kubectl-convert: FAILED
   sha256sum: WARNING: 1 computed checksum did NOT match
   ```

   {{< note >}}
   बाइनरी और चेकसम का एक ही संस्करण डाउनलोड करें.
   {{< /note >}}

1. इंस्टॉल kubectl-convert

   ```bash
   sudo install -o root -g root -m 0755 kubectl-convert /usr/local/bin/kubectl-convert
   ```

1. सत्यापित करें कि प्लगइन सफलतापूर्वक इंस्टॉल है

   ```shell
   kubectl convert --help
   ```

   यदि आपको कोई त्रुटि नहीं दिखाई देती है, तो इसका मतलब है कि प्लगइन सफलतापूर्वक इंस्टॉल हो गया है.

## {{% heading "आगे क्या होगा" %}}

{{< include "included/kubectl-whats-next.md" >}}
