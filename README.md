[![crates.io](https://img.shields.io/crates/v/winrt.svg)](https://crates.io/crates/winrt)
[![docs.rs](https://docs.rs/winrt/badge.svg)](https://docs.rs/winrt/0.7.0/winrt)
[![Build and Test](https://github.com/microsoft/winrt-rs/workflows/Build%20and%20Test/badge.svg?event=push)](https://github.com/microsoft/winrt-rs/actions)

## The Rust/WinRT language projection

Rust/WinRT follows in the tradition established by [C++/WinRT](https://github.com/microsoft/cppwinrt) of building language projections for the Windows Runtime using standard languages and compilers, providing a natural and idiomatic way for Rust developers to call Windows APIs. Rust/WinRT lets you call any WinRT API past, present, and future using code generated on the fly directly from the metadata describing the API and right into your Rust package where you can call them as if they were just another Rust module.

The Windows Runtime is based on Component Object Model (COM) APIs under the hood and is designed to be accessed through language projections like C++/WinRT and Rust/WinRT. Those language projections take the metadata describing various APIs and provide natural bindings for the target programming language. As you can imagine, this allows developers to more easily build apps and components for Windows using their desired language. You can then use those Windows APIs to build desktop apps, store apps, or something more unique like a component, NT service, or device driver.

## Getting started

Start by adding the following to your Cargo.toml file:

```toml
[dependencies]
winrt = "0.7"
```

This will allow Cargo to download, build, and cache the Rust/WinRT support as a package directly from crates.io.

```rust
use winrt::*;

// Now use the `import` macro to import the desired winmd files and types:
import!(
    dependencies
        os
    types
        windows::data::xml::dom::*
        windows::foundation::*
        windows::ui::*
);

// Finally, make use of any WinRT APIs as needed. For example, here is
// an example of using the `XmlDocument` class to parse an XML document:
fn main() -> Result<()> {
    use windows::data::xml::dom::*;

    let doc = XmlDocument::new()?;
    doc.load_xml("<html>hello world</html>")?;

    let root = doc.document_element()?;
    assert!(root.node_name()? == "html");
    assert!(root.inner_text()? == "hello world");

    Ok(())
}
```

For a more complete example, take a look at Robert Mikhayelyan's [Minesweeper](https://github.com/robmikh/minesweeper-rs).

## Safety

We believe that WinRT bindings can map to 100% safe Rust. However, often times WinRT APIs are implemented in non-memory safe languages (e.g., C++). WinRT-rs users can be sure that (modulo bugs), the WinRT interop bindings generated by this crate are 100% safe to use. Users should, however, make sure that the code that implements the APIs being called through WinRT-rs bindings are either written in safe Rust or have been audited for memory safety and correctly adhere to the WinRT contract. WinRT APIs written in safe Rust and consumed from Rust using WinRT-rs should therefore be 100% memory safe.

We take these safety gurantees very seriously. Please let us know if you run into issues where you see Rust's memory safety guarantees being violated through use of this crate. 

## Backwards compatibility 

This project is under heavy development. Both the `winrt` crate and `cargo-winrt` are expected to have many breaking changes before reaching 1.0. Until then, breaking changes will be signified by a bump in the 0.X (e.g., 0.7.0 to 0.8.0) version number meaning that 0.X.Y (e.g., 0.7.0 to 0.7.1) version bumps will only happen when there are no breaking changes. 

Other crates under the WinRT-rs umbrella such as `winrt_gen` and `winrt_macros` are considered implementation details at this point, and breaking changes can happen at any time even in 0.X.Y version bumps. These crates will generally follow in lockstep with the version of `winrt`. 

## IDE support

WinRT is developed with and tested for the [rust-analyzer](https://marketplace.visualstudio.com/items?itemName=matklad.rust-analyzer) VS Code extension. We want WinRT-rs to work well with rust-analyzer, so if you run into issues, please file a bug report so we can look into it. There are, however, a few things to note about WinRT-rs and rust-analyzer:

* WinRT-rs can sometimes generate _a lot_ of code causing rust-analyzer to slow down. This can mean it may take upwards of a several minutes when running rust-analyzer for the first time on a code base where large WinRT namespaces are used. Additionally, it may take a second for some code completion hints to show. These are known issues. We hope to work with the rust-analyzer team to address these performance concerns. 
* If you generate code using a `winrt::build!` in a build script, it is important to turn on the "rust-analyzer.cargo.loadOutDirsFromCheck" setting in VS Code. This runs `cargo check` so that rust-analyzer is aware of generated code in the `OUT_DIR`. Leaving this setting off will cause rust-analyzer to not recognize the generated namespaces breaking code completion. 

## Cross-platform support

While support for platforms other than Windows (i.e., *-windows-msvc targets) is possible and on the long-term roadmap, support outside of Windows is not an immediate goal. If you are interested in support outside of Windows, please [let us know](https://github.com/microsoft/winrt-rs/issues/143).

## Project Reunion

Project Reunion is a set of libraries, frameworks, components, and tools that you can use to access powerful Windows platform functionality from all kinds of apps on many versions of Windows. Project Reunion combines the power of Win32 native applications alongside modern APIs, so your apps light up everywhere your users are.

Other Project Reunion components include [WinUI](https://github.com/microsoft/microsoft-ui-xaml), WebView2, MSIX, [C++/WinRT](https://github.com/microsoft/cppwinrt), and [C#/WinRT](https://github.com/microsoft/cswinrt). If you'd like to learn more, contribute to Project Reunion, or have app model questions, visit [Project Reunion on GitHub](https://github.com/microsoft/ProjectReunion).
