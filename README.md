# Custom-KWS-for-STM32-using-Edge-Impulse

This guide will help you deploy a custom KWS system on any STM32 MCU (in this tutorial we will be using STM32F44NRE). [Edge Impulse](https://www.edgeimpulse.com/) enables developers to create their own intelligent device solutions with embedded Machine Learning. You will learn how to easily acquire wav dataset samples using your own voice, train your ML algorithm and deploy to your devices. 

# Requirements

- [Stm32 NUCLEO boards](https://www.st.com/en/evaluation-tools/stm32-nucleo-boards.html)
- Sign up for a free [Edge Impulse account](https://www.edgeimpulse.com/) 
- [Stm32IDE development tool](https://www.st.com/en/development-tools/stm32cubeide.html)

# Prerequisites

Make sure you followed the [Home Automation keywords tutorial](Home-Automation-traininig/README.md) A, and have a trained impulse.

Creating your Edge Impulse project
In this tutorial we'll build a model that can distinguish between different type of shoes: flip flops, sneakers or running shoes.

# Data-collection
