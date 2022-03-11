# BatchCrypt: Efficient Homomorphic Encryption for Cross-Silo Federated Learning

Created: March 7, 2022 3:55 PM
Last Edited Time: March 7, 2022 4:50 PM
Status: In Review
Type: Federated Learning

# BatchCrypt: Efficient Homomorphic Encryption for Cross-Silo
Federated Learning

## Abstract

- Cross-silo federated learning (FL): big organization train machine learning model together (# clients ~ 10, each clients hold a lot of data)
- To ensure secure aggregation, industrial FL frameworks uses additively homomorphic encryption (HE).
- However, this results in significant cost in computation and communication: HE operations dominate the training time
- In this paper, we present BatchCrypt, a system solution for cross-silo FL that substantially **reduces** the encryption and communication overhead caused by HE:
    - Instead of encrypting individual gradients with full precision, we encode a batch of quantized gradients into a long integer
    - Encrypt it in one go.
    - New quantization and encoding schemes along with a novel gradient clipping technique.

## Introduction

- Data are held by multiple parties and the parties don’t want to share
- Cross-silo Federated Learning provides an approach that help parties jointly train the ML model without revealing their data.
- Additive HE, particularly Paillier cryptosystem, provides a way to securely aggregate the weight updates among clients.
- Two problems:
    - HE under Paillier cryptosystem involves expensive computation
    - Resulted ciphertexts are often large in size
- One solution:
    - Quantized the gradient update, and update them in one go!
- Two technical challenges in the solution:
    - nature of quantized gradients is not fit for batch encryption —> need a new quantization algorithm
    - how to choose the clipping threshold for clipping the gradient before quantization

## Background and Related Work

1. Cross-silo FL
2. Privacy Solutions in FL
    1. MPC
    2. DP
    3. Secure Aggregation
    4. Homomorphic Encryption
3. Cross-Silo FL Platform with Homomorphic Encryption

## Source of Problems

Come from the fact that HE based on Paillier cryptosystem is heavy on both computation and communication if we takes floating point number as input.

## BatchCrypt

1. Why HE Batching for FL a problem?
    
    Such a generic quantization scheme, though simple to implement, does not support aggregation well and has many limitations when applied to batched gradient aggregation.
    
    - It is restrictive. In order to de-quantize the results, it
    **must** know *how many values* are aggregated. This poses extra
    barriers to flexible synchronization, where the number of
    updates is constantly changing, sometimes even unavailable.
    - It overflows easily in aggregation. As values are quantized into positive integers, aggregating them is bound to overflow quickly as the sum grows larger. To prevent overflow, batched ciphertexts have to be decrypted after a few additions and encrypted again in prior work [48].
    - It does not differentiate positive overflows from negative. Once overflow occurs, the computation has to restart. (Should we be able to tell them apart, a saturated value could have been used instead of discarding the results).
2. HE Batching for Gradient

**Design Goals:**

- **Signed Integers:** Quantize grad to signed integers instead of unsigned one.
- **Symmetric Range:** Quantize range must be symmetrical so as to allow positive and negative to cancel each other out.
- **Uniform Quantization:** As addition over quantized values are required

**BatchCrypt:**

- Encode the signed bits as we use 2-complement method for representing numbers.
- Analytical Clipping