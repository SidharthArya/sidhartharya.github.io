# Towards efficient models for real-time deep noise suppression


## Notes {#notes}


### ABSTRACT {#abstract}

-   Reasonably small recurrent and [Convolutional Neural Networks]({{< relref "20210721174649-neural_networks.md#7947dad5-fb28-479e-9167-ebeb092e504f" >}})

architectures on large dataset.

-   Reverberation was also considered
-   Evaluated [Convolutional Neural Networks]({{< relref "20210721174649-neural_networks.md#7947dad5-fb28-479e-9167-ebeb092e504f" >}}) architectures
-   Trade off between computational complexity and the achievable
    speech quality


### INTRODUCTION {#introduction}


#### Models {#models}

-   [Recurrent Neural Networks]({{< relref "20210721174649-neural_networks.md#5d677af3-e64f-4b0d-b854-84fdd6592d71" >}}) - efficient temporal modeling
    capabilities but hits performance saturation
-   [Convolutional Recurrent Networks]({{< relref "20210721174649-neural_networks.md#c161d52d-3c17-4f1f-af75-f11dc9a4cdd0" >}}) and [Convolutional Neural Networks]({{< relref "20210721174649-neural_networks.md#7947dad5-fb28-479e-9167-ebeb092e504f" >}})
     raised performance but enormously large architectures
    NOTE: (with ref: ([Luo and Mesgarani 2019](#org-target--citeproc-bib-item-1)) ) time domain networks can
     in **THEORY** produce better results than frequency domain networks
     but not yet realized.


#### Goal {#/home/arya/Downloads/Papers/2101.09249.pdf-annot-1-3}

which network parts can be scaled, removed, or replaced by more efficient modules, at which gains in complexity and which loss in quality. Specifically, we investigate the influence of RNN size, type, and the use of disconnected parallel RNNs. For CRNs with a symmetric convolutional encoder/decoder structure, we investigate the convolution layers, spectral vs. spectro-temporal convolutions, and skip connections. As a result, we propose an efficient CRN structure with around 4-5 times less computational operations with similar quality than previously proposed CRNs.


### ENHANCEMENT SYSTEM AND TRAINING OBJECTIVE {#enhancement-system-and-training-objective}

-   Spectral Suppression based enhancement systems - robust
    generalization, logical interpretation and control, and easier
    integration with existing speech processing algorithms.
-   Input Features are **log power spectra**
-   Output is a real-valued, time varying suppression gain per time
    frequency bin
-   To compute a single frame, the network requires only the features
    of the current frame, or when using causal convolution, also
    several past frames. So latency depends only on the STFT window
    size.
-   STFT consistency is enforced by propagating FD output through
    reconstruction and another STFT to compute a FD loss.

    {{< figure src="Notes/2021-07-06_11-59-50_2021-07-06-115330_757x265_scrot.png" >}}

-   Both Predicted and target signals are **normalized** by the active
    target utterance level, to enure balanced optimization for signal
    level dependent losses.

-   MSE is used, blending the magnitude only with a phase aware term,
    which is found to be superior to other losses for reverberant
    speech enhancement.
    Loss function is given by
    \\[L = (1-\lambda)\Sigma\_{k,n}||S|^c - |\hat S|^c|^2 + \lambda
         \Sigma\_{k,n}||S|^c e^{j\psi s} - |\hat S|^c e^{j \psi s}|^2\\]
    where c = 0.3 is a compression factor, &lambda; = 0.3 is a
    weighting between complex and magnitude based loss, and omitted
    the dependency of the target speech spectral bins S(k,n) on the
    frequency and time indices k, n for brevity.

-   The networks are trained in batches of 10 sequences of 10 s
    length using the AdamW optimizer, learning rate of \\[8 \*
         10^{-5}\\] and weight decay of 0.1.

-   The best model is picked based on the validation metric using a
    heuristic optimization criterion using perceptual evaluation of
    speech quality, scale invariant signal to noise ration and
    cepstral distance
    \\[ Q = PESQ + 0.2 \* siSRD - CD\\]


### NETWORK ARCHITECTURES {#network-architectures}


#### NSnet2 {#nsnet2}

-   Consists only of fully connected and gated recurrent unit layers in

the form
FC-GRU-GRU-FC-FC-FC.

-   All FC layers are followed by ReLU activation
-   Except the last layer has sigmoid activations to predict
    constrained suppression
-   The standard layer dimensions are 400 for GRUs and 600 for FC
    layers i/e/ 400-400-400-600-600-K
-   Also investigated different configurations
-   The input and output dimensions are the number of frequency bins.


#### CRUSE {#cruse}

-   CRN U-Net structure
-   Convolutional Recurrent U-net for Speech Enhancement (CRUSE)

{{< figure src="Notes/2021-07-06_15-10-38_2021-07-06-150859_837x528_scrot.png" >}}

-   L symmetric convolutional and deconvolutional encoder and
    decoder layers with kernels of size (2,3) in time an frequency
    dimensions. Convolution kernels move with a stride of (1,2)
    i.e. downsample the features along the frequency axis
    efficiently while the number of channels C<sub>l</sub> for layer
    {1,...,L} increase per encoder layer, and decrease mirrored in
    the decoder. In this work the input and output channels C<sub>in</sub>
    = C<sub>out</sub> = 1 but they can be extended to e.g. take complex
    values or multiple features as multiple
    channels. Convolutional layers are followed by leaky ReLU
    activations while the last layer uses sigmoid. Between encoder
    and decoder sits a recurrent layer, which is fed with all
    features flattened along channels.
-   Using 1 GRU layer over 2 LSTM layers at this stage leads to
    **little performance loss**, but **huge computational savings**
-   GRU saves 25% complexity compared to an LSTM layer.

**Further Modifications**

-   Parallel RNN grouping
    Performance of both CRUSE and NSNet2 directly scales with
    bottleneck size i.e. the width R of the central RNN
    layers. However, the complexity of RNN layers scales with R^2
    making wide RNNs computational unattractive. There for RNNs
    are grouped into P disconnected parallel RNNs, still yielding
    the same forward information flow.
    -   Possible parallel execution of disconnected RNNs.

-   Skip Connections
    Use skip connection by adding rather than concatenating.

    -   More effective
    -   Minor Performance degradation

    Also added a trainable channel wise scaling and bias in the
    add skip connection implemented as convolutions

{{< figure src="Notes/2021-07-06_15-53-22_2021-07-06-155308_825x311_scrot.png" >}}


### EXPERIMENTAL SETUP {#experimental-setup}


#### Dataset {#dataset}

-   large scale synthetic training set and test on real recordings
-   544 hours of high mean opinion score
-   Most data open sourced
    -   T_{60] &gt; 0.22s and C<sub>50</sub> &lt; 18 dB implies reverberation
-   **Data Generation Pipeline**

{{< figure src="Notes/2021-07-06_16-13-50_2021-07-06-161315_763x470_scrot.png" >}}

-


#### Algorithmic parameters {#algorithmic-parameters}

-   16kHz sampling frequency
-   STFT with 50% overlapping squareroot-Hann windows of 20ms length
-   FFT size of 320 points
-   Input is 161 dimensional log power spectra
-   NSnet2-R where R denotes the number of GRU node per layer
-   parameterize CRUSE with different encoder decoder sizes,
    starting alway with C_1 = 16 channels, and doubling the channels
    each layer.
-   CRUSE L-C_L - NxRNNP, where L are the number of E-D layers, the
    last encoder layer filters C_L can vary to scale the RNN layer
    width, N are the number of RNN layers and P are the number of
    parallel RNNs.
-   Convolution kernels are always (2,3), unless denoted explicitly
    as 1D convolutions with (1,3) kernels operating only across
    frequency.


### RESULTS {#results}


### CONCLUSIONS {#conclusions}

-   Speech Quality is a function of network size, especially
    depending on the recurrent layer width


## Bibliography {#bibliography}

<span class="org-target" id="org-target--citeproc-bib-item-1"></span>Luo, Yi, and Nima Mesgarani. 2019. “Conv-Tasnet: Surpassing Ideal Time-Frequency Magnitude Masking for Speech Separation.” _IEEE/ACM Transactions on Audio, Speech, and Language Processing_ 27 (8):1256–66. <https://doi.org/10.1109/taslp.2019.2915167>.
