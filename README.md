PaddleSleeve
===
简体中文 

**PaddleSleeve是基于百度开源深度学习平台飞桨的安全与隐私工具**，为AI产业实践提供丰富、有效、易用的模型安全及隐私评测及保护能力。

PaddleSleeve作为一款贴近实践应用与现实风险的AI安全和隐私工具，具备以下特性：

- **丰富的AI应用场景**：支持多种现实AI任务，包含图像分类、目标识别等
- **现实的AI安全及隐私风险**：针对对抗样本、自然及环境干扰、模型推断、模型窃取等高危风险，提供全面的检测评估
- **支持产业级模型**：支持对ResNet、YOLO、PaddleHub预训练模型等产业级模型进行安全和隐私加固


## 主要功能

PaddleSleeve融合了业界最前沿的攻击方法与策略，用于评估模型的安全与隐私性能，并拥有全面、灵活的安全与隐私增强手段。其主要功能包括：

- **模型攻击与评估**
  - 鲁棒性（对抗场景）：集成最新的黑白盒对抗样本攻击算法，测试和评估模型在对抗场景下的鲁棒性。
  - 鲁棒性（非对抗场景）：使用光照、噪点、天气在内的多种拟自然扰动算法，测试和评估模型在非对抗场景下的鲁棒性。可以跨框架进行模型比较，如将用户的飞桨模型与pytorch、keras模型进行比较。
  - 隐私性：基于梯度泄露和生成对抗网络的侧信道逆向攻击，测试模型数据被还原的风险；基于影子模型的黑盒推断与重构攻击算法，测试模型是否存在关键信息泄露风险；支持AUC、Recall、结构相似度、峰值信噪比（Peak SNR）等隐私攻击效果评估指标。
- **模型防御**
  - 对抗训练：支持新训练、模型精调等方式进行对抗训练，支持多个业界前沿、性能良好的对抗训练方法，如TRADES、AWP等。
  - 图像重建/噪声过滤：提供多种对抗噪声过滤算法，实现非侵入式的对抗鲁棒性增强，无需修改模型。
  - 隐私增强优化器：提供基于差分隐私扰动、梯度压缩等方法的一系列隐私增强优化器（如SGD、Adam、Momentum等），可便捷地训练出有效抵御常见隐私窃取攻击的模型。
  - 代码审计：通过分析源代码、模糊测试等方式，挖掘代码中存在的安全缺陷。
  - 端模型保护：基于ARM TrustZone技术，实现移动端模型参数明文不出TEE（安全域），在TEE中进行解密和运算。


## 技术特色

- **融合业界前沿的AI安全与隐私攻击技术：**
  - 集成多个业界前沿的对抗攻击算法、自研的拟自然扰动算法，利用自动化攻击策略，有效测试模型在对抗及非对抗场景下的鲁棒性。
  - 集成多个业界前沿的模型数据推断、数据逆向、参数窃取算法，有效测试模型在私有化部署、对外服务、联合训练等场景下的隐私泄露风险。
- **全面、灵活、易用的安全与隐私增强手段：**
  - 嵌入飞桨训练阶段的安全与隐私增强手段：包括多个业界前沿的对抗训练算法、隐私增强优化器等，安全和隐私性能提升明显。
  - 非侵入式安全与隐私增强手段：包括去噪、特征压缩、图像恢复、模糊化等方法，易于使用，可直接用于已有模型或飞桨预训练模型，无需重训练即可提升模型安全和隐私等级。


## 对抗样本生成与防御 AdvBox 

近年来，深度学习技术发展迅速，尤其是在计算机视觉领域内，深度学习有了越来越多的落地场景和应用场景。

对抗样本攻击（Adversarial Example Attack）可以在正常数据输入的基础上精心构造足够小的扰动，在肉眼几乎无法察觉的情况下，导致AI模型识别错误。一些如对抗贴图（Adversarial Patch）的对抗样本形式，干扰图像明显，但不影响人类的识别。目前除了图像分类，人脸识别、目标识别、 图像分割、语音识别、语义理解等各种任务都可能受到对抗样本的干扰。

Advbox收集整理了各种对抗样本的生成方法及模型加固方法，便于Paddle的开发者、使用者能更好地发现和降低模型健壮性的风险敞口。Advbox支持多种对抗样本生成的经典算法，如FGSM、BIM、PGD、hopskipjump和C/W等方法。

目前对抗训练是提升模型鲁棒性的主要方法，需要在训练过程中加入对抗样本到正常样本中，Advbox有相关对抗训练的示例。而且，Advbox提供了去除对抗噪的多种算法，可以方便的对输入图像进行预处理，提升对扰动图片的识别效果。

## 鲁棒性评测 Robustness

Robustness模块主要基于现实意义对模型鲁棒性进行评估。对图像进行的变换主要有：空间变换、色彩、明暗、天气等，模拟真实世界的失真、变形、噪点等。

模型鲁棒性有三种应用场景：一般应用场景、强对抗场景（如内容审核）、安全攸关（safety-critical）场景（如自动驾驶），模型鲁棒性在后两种场景下更为重要。例如，在安全攸关场景中，一些干扰的出现是极端环境的小概率事件，在数据集占比极小，甚至可能都不会存在 ，例如翻车后的车底图片、强光照耀等，但由于模型可能未经过此类数据的训练与评估，在强调安全的场景下其鲁棒性会受到质疑，模型判断失误也会造成更大的损失。

## 模型隐私风险测试与评估 PrivBox

PrivBox是基于PaddlePaddle的AI隐私安全性测试工具库。AI模型往往存在着一定的隐私泄露风险，攻击者可以通过各种手段从模型中获取原始训练数据的信息，或者推断出训练数据的某些特征。PrivBox从攻击检测的角度，提供了多种AI模型隐私攻击的前沿成果的实现，攻击类别包括模型逆向、成员推理、属性推理、模型窃取等，可以帮助模型开发者们针对实际场景特点来更好地评估自己模型的隐私风险。

目前提供的具体隐私攻击算法包括：

- 模型逆向：包括了基于生成对抗网络（GAN）的逆向方法，以及基于深度梯度还原的逆向方法（DLG）；
- 成员推理：包括基于影子模型方式的ML-Leaks攻击方法，以及基于规则攻击的Rule-based攻击方法；
- 模型窃取：提供了Knock-off Nets模型窃取攻击方法；

另外，PrivBox还提供了一系列丰富的指标，用来反映模型在隐私攻击下泄露风险，比如：ACC、AUC、RECALL、PSNR（峰值信噪比）、SSIM（结构相似度）等。

## 隐私增强 PrivacyGuard ##

PrivacyGuard在模型的隐私风险检测与评估的基础上，提供了一系列隐私增强方法，以便于用户根据风险类型和实施阶段来定制合适的防护方案。针对模型隐私攻击的增强方法一般采用差分隐私、梯度压缩等技术，从而提升在模型训练过程中窃取隐私信息的难度；或者采用置信度模糊的方法，增加从模型输出结果中推断原始训练数据的难度。

目前PrivacyGuard所提供的隐私增强方法有：

- 基于差分隐私的方法：包括带差分隐私扰动的各种优化器，如DP-SGD、DP-Adam、DP-Momemtum、DP-Adagrad等，主要用于防范模型逆向、成员推理、参数窃取等攻击；

- 基于梯度压缩的方法：包括Deep-Gradient-Compression Momentum等，主要用于防范模型逆向攻击；

- 基于置信度模糊化的方法：包括Labeling、Rouding、TopK等模糊化手段，主要用于防范成员推断等攻击。

## 代码安全审计 CodeSecurity ##

CodeSecurity为飞桨深度学习框架提供了代码漏洞挖掘的方法与相关示例。飞桨等深度学习框架主要由Python和C++编写，可能存在堆栈溢出、数组越界等代码安全缺陷，会对框架的安全性与稳定性产生重要影响。CodeSecurity模块通过分析源代码、模糊测试等方式，充分挖掘框架中存在的安全缺陷以及规范性缺陷，并将漏洞挖掘方案贡献开源社区，以期与社区众多开发者和安全研究者一同提升飞桨框架代码的代码质量与安全性。

目前，CodeSecurity模块包含两个部分：

- Fuzzing：采用atheris+libfuzzer，对PaddlePaddle API进行模糊测试，动态、快速地发现框架在运行中存在的漏洞；

- 代码审计：基于静态分析框架CodeQL，实现针对特定漏洞类型的QL，高效、一次性地找出同类型所有漏洞。
  
## 端模型保护框架 Paddle-LiTEE ##

Paddle-LiTEE适用于具备ARM TrustZone安全能力的移动设备。基于ARM TrustZone技术，实现模型参数明文不出TEE（安全域），在TEE中进行解密和运算。Paddle-LiTEE分为模型加密工具和端侧推理框架两部分。模型开发者使用模型加密工具，在ARM TrustZone中加密模型，并生成配置文件。端侧推理时，加载加密后的模型，在TEE中解密，并进行相关算子运算。

## 版权和许可证
PaddleSleeve由[Apache-2.0 license](LICENSE)提供。

