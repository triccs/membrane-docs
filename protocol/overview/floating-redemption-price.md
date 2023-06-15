# Floating Peg

Membrane uses logic similar to a PID, pioneered by Reflexer Labs, which we call the **Credit Price Controller** to regulate the redemption price (peg) of CDT. The redemption price an "interest rate" or redemption rate that moves inverse to market conditions. \
\
**Ex 1:** At a redemption price of 1.00, if CDT's market price is .95, then its redemption rate will be 5%. In other words, in a year at similar market conditions, the redemption price would be 1.05.\
\
**Ex 2:** On the flipside, if the market price is 1.05 at the same redemption price of 1.00, then the rate is _**negative**_ 5% or a redemption price of .95 in a year.\


The **redemption price** may not directly affect market price, but it **influences the health of all vaults.**&#x20;

This system adopts a wider volatility range for long-term system optimality, decreasing the reliance on centralized collateral (i.e. existential risk). Some have called negative redemption rates, "paying for decentralization". Without low volatility collateral, typically USDC, the peg trends above 1.00. Redemption rates & low volatility assets are tools to help the system repeg in an attempt to remove single reliance on either.

The existance of a floating peg also enables interesting peg denomination functionality, though not launch applicable. The system is built to host multiple Baskets of collateral types & parameters, allowing for **CDT** to have loans denominated in multiple currencies or collateral type specific Baskets to ease programmability. Combine this with the dynamic supply cap & redemption price rates and **CDT** has the capability to be pegged to a basket of market driven Units of Account

_Additional resources for understanding of the first floating peg system RAI:_

[https://medium.com/reflexer-labs/stability-without-pegs-8c6a1cbc7fbd](https://medium.com/reflexer-labs/stability-without-pegs-8c6a1cbc7fbd)\
[https://twitter.com/ameensol/status/1420048205127946246?s=20\&t=VVBsx8gveHSZr6hWhzIrNA](https://twitter.com/ameensol/status/1420048205127946246?s=20\&t=VVBsx8gveHSZr6hWhzIrNA)&#x20;
