---
description: >-
  Collateral markets separate from the minting mechanism that allow Membrane to
  offer more exotic collateral types and isolated risk profiles, think
  Morpho/Euler
---

# Isolated Markets

### Overview

The isolated markets are individually managed & contain unique collateral parameters (caps, LTV, Interest rate) for each market. These markets are specifically for pure borrowers and pure lenders since supplied collateral isn't also lent out, removing the ability for borrower-lender users to emerge.

### Risk Tranches

Risk tranches are a. way for debt suppliers to customize their risk profile within a market. Typically if you lend to a market, you take on all insolvency risk of that making (i.e. losing money if the borrowers can't repay). Tranches, specifically senior & junior tranches, allow lenders to choose whether they want to be on the frontlines of risk taking for added reward, or if they want to be the last line of defense for a more consistent salary.

Seniors get a fixed rate and are the last lending pool to be assigned bad debt from borrowers. Juniors get everything left over from the seniors' fixed rate allocation but are the first to lose capital from bad debt.\
\
In the event where the interest rate can't pay the seniors' fixed rate due to costs and market configurations, the seniors will get 80% of the rate revenue & the juniors will get the remaining 20%.

### Roadmap

We'd like isolated markets to act as a Proving Ground for new collateral types where the market can autonomously search for the optimal LTVs and interest rates for new collateral.

Our expected mechanism for LTV seeking is allowing the junior tranche suppliers to choose any LTV higher than the manager's configured LTV. Because they take direct risk from bad debt in the market, this allows them to increase their risk to reward ratio while providing borrowers with higher LTVs (i.e. capital efficiency).
