---
layout: post
date: 30-01-2018
author: Andrzej Jóźwiak
title: Playing with ETA (draft)
tags: jvm haskell eta
disqus: true
---

Some things that can be poked around in the tutorial section, cool!
```haskell
curry2 :: ((a, b) -> c) -> (a -> b -> c)
curry2 bif = \a -> \b -> bif (a, b)

addP :: (Int, Int) -> Int
addP pi = fst pi + snd pi

addC2 :: Int -> Int -> Int
addC2 = curry2 addP

add5 :: Int -> Int
add5 = addC2 5

main :: IO ()
main = do
      print (add5 10)
```

```haskell
-- Generic Pair
data GPair a b = GPair a b

myGPair :: GPair String String
myGPair = GPair "test" "pary"

gfst :: GPair a b -> a
gfst gp =
  case gp of
     GPair x _ -> x

gsnd :: GPair a b -> b
gsnd gp =
  case gp of
     GPair _ y -> y

gpairToStr :: (Show a, Show b) => GPair a b -> String
gpairToStr gp = "(" ++ show (gfst gp) ++ "," ++ show (gsnd gp) ++ ")"

data Pair = Pair Int Int

myPair :: Pair
myPair = Pair 1 2

pairToString :: Pair -> String
pairToString p =
  case p of
    Pair x y -> "(" ++ show x ++ "," ++ show y ++ ")"

main :: IO ()
main = do
  putStrLn $ "myGPair = " ++ gpairToStr myGPair
  putStrLn $ "myPair = " ++ pairToString myPair
```
