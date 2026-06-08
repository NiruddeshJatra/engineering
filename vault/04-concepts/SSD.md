---
tags:
  - medium
source: "[[crash-course-cs-ep19]]"
---
## Definition

A persistent, non-volatile storage device made entirely of flash memory chips. It holds your data when the power is turned off, with absolutely zero moving parts.

## Details

- **The Big Win:** No moving pieces. Old hard drives ([[HDD]]s) are basically mechanical record players with spinning magnetic platters and a tiny arm that has to physically fly around to find data. SSDs are just silent microchips.
    
- **Goodbye "Seek Time":** Because there’s no physical arm moving around, the latency to find a random byte of data drops from milliseconds (eternity for a CPU) to microseconds.
    
- **The Catch (Wear and Tear):** You can read from an SSD infinitely, but writing to it physically degrades the chips over time. The drive's internal controller has to do "wear leveling" (spreading writes evenly across the chips) so the drive doesn't burn out too fast.
    

## Why it matters to me as a dev

- **The DB Performance Revolution:** Before SSDs, doing random database lookups across millions of rows was an architectural nightmare because the physical HDD head had to bounce around the disk. SSDs completely changed database design—random reads became insanely cheap, which is why modern cloud databases perform so well out of the box.
    
- **Cloud Costs (Hot vs. Cold Storage):** SSDs are fast but way more expensive per gigabyte than HDDs. When configuring servers on AWS or Azure, this dictates your budget. You put your active database on expensive SSD storage (Hot Data) and dump your logs or system backups on cheap, slow HDD storage or S3 buckets (Cold Data).
    

## Open questions
- I tried to understand how SSD and HDD work, but failed. I think they are too difficult for me to understand and unnecessary for my course.

## In my words
SSD is a kind of non-volatile storage which is fast and expensive than HDD. Reading and writing from SSD is super fast because there is no physical or mechanical movement happening, just passing voltage works. 