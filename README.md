Elm Bridge
=====

[![Build Status](https://travis-ci.org/agrafix/elm-bridge.svg)](https://travis-ci.org/agrafix/elm-bridge)

[![Hackage Deps](https://img.shields.io/hackage-deps/v/elm-bridge.svg)](http://packdeps.haskellers.com/reverse/elm-bridge)

## Intro

Hackage: [elm-bridge](http://hackage.haskell.org/package/elm-bridge)

Building the bridge from [Haskell](http://haskell.org) to [Elm](http://elm-lang.org) and back. Define types once, use on both sides and enjoy easy (de)serialisation. Cheers!

Note that the [bartavelle/json-helpers](http://package.elm-lang.org/packages/bartavelle/json-helpers/latest/) package, with version >= 1.1.0, is expected by the generated Elm modules.

## Usage

```haskell
{-# LANGUAGE TemplateHaskell #-}
import Elm.Derive
import Elm.Module

import Data.Proxy

data Foo
   = Foo
   { f_name :: String
   , f_blablub :: Int
   } deriving (Show, Eq)

deriveBoth defaultOptions ''Foo

main :: IO ()
main =
    putStrLn $ makeElmModule "Foo"
    [ DefineElm (Proxy :: Proxy Foo)
    ]

```

Output will be:

```elm
module Foo where

import Json.Decode
import Json.Decode exposing ((:=))
import Json.Encode
import Json.Helpers exposing (..)


type alias Foo  =
   { f_name: String
   , f_blablub: Int
   }

jsonDecFoo : Json.Decode.Decoder ( Foo )
jsonDecFoo =
   ("f_name" := Json.Decode.string) `Json.Decode.andThen` \pf_name ->
   ("f_blablub" := Json.Decode.int) `Json.Decode.andThen` \pf_blablub ->
   Json.Decode.succeed {f_name = pf_name, f_blablub = pf_blablub}

jsonEncFoo : Foo -> Value
jsonEncFoo  val =
   Json.Encode.object
   [ ("f_name", Json.Encode.string val.f_name)
   , ("f_blablub", Json.Encode.int val.f_blablub)
   ]
```

For more usage examples check the tests or the examples dir.

## Install

* Using cabal: `cabal install elm-bridge`
* From Source: `git clone https://github.com/agrafix/elm-bridge.git && cd elm-bridge && cabal install`
