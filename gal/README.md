# GAL glue code

A GAL22V10 is used for address decoding for various devices. See 
`decoder.pld` for details.

## Prerequisites

Build galasm from https://github.com/daveho/GALasm

## Build and Burn

  galasm decoder.pld

then use minipro to write

  minipro -p "GAL22V10D" -w ./decoder.jed
