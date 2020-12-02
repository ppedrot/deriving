# Deriving ─ Generic instances for Coq inductive types

[![arthuraa](https://circleci.com/gh/arthuraa/deriving.svg?style=shield)](https://circleci.com/gh/arthuraa/deriving/tree/master)

The Deriving library builds instances of basic MathComp classes for inductive
data types with little boilerplate, akin to Haskell's `deriving` functionality.
To define an `eqType` instance for a type `foo`, just write:

    From mathcomp Require Import ssreflect ssrnat eqtype.
    From deriving Require Import deriving.

    Inductive foo := Foo1 of nat | Foo2 of foo & nat.

    (* foo_rect is the recursor automatically generated by Coq *)
    Definition foo_indDef := [indDef for foo_rect].
    Canonical foo_indType := IndType foo foo_indMixin.

    Definition foo_eqMixin := [derive eqMixin for foo].
    Canonical foo_eqType := EqType foo foo_eqMixin.

## Supported types and limitations

Besides simple definitions such as the one above, Deriving can handle the
following features:

- Types with uniform parameters (e.g. `list`).

- Mutually inductive types (by using the recursor generated by `Combined Scheme`
  command).

- Nested inductive types (if you write your own recursor).

Check the `tests/` directory for detailed examples.

The following features are still not supported:

- Types with non-uniform parameters (e.g. `Vector.t`).

- Constructors with dependent types (e.g. `C : forall n : nat, P n -> T`).

- Coinductive types.

## Predefined instances

Besides `eqType`, there are predefined generic instances for `choiceType`,
`countType`, `finType` and `orderType`.  To use them, you must ensure that every
non-recursive argument of the type is _also an instance of the class_;
otherwise, you'll get an ugly, uninformative error message.  For `finType`, you
must additionally ensure that the type does not have recursive occurrences.

You can also define instances for your own classes inside of Coq, without
resorting to OCaml code.  This feature is not documented yet, but you can refer
to the definition of the instances provided by Deriving in
`theories/instances`. Or drop me a line!

## Simplification and performance issues

It is useful for instances of certain classes to have good reduction behavior
(e.g. `eqType`).  By default, Deriving attempts to simplify the derived
instances as much as possible, to make them more similar to hand-written code.
However, this process can be too slow for large definitions, so it can be
disabled with the `nored` flag:

    Definition large_type_eqMixin : Equality.mixin_of large_type.
    Proof. exact [derive nored eqMixin for large_type]. Qed.

In such cases, it is a good idea to keep the instance opaque (e.g. defined with
`Qed`) to avoid slowdown.

## Requirements

- Coq 8.10 -- 8.12

- `coq-mathcomp-ssreflect` 1.10 -- 1.11

## Installation

Deriving can be installed through the
[`extra-dev`](https://coq.inria.fr/opam/extra-dev/README.md) repository:

```shell
opam repo add coq-extra-dev https://coq.inria.fr/opam/extra-dev
opam install coq-deriving
```

Alternatively, you can compile Deriving from source:

```shell
make && make install
```
