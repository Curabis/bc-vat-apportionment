# VAT apportionment for Business Central (Danish splitmoms)

Danish businesses with both VAT-liable and VAT-exempt turnover may only deduct part of the input VAT on common costs (Danish VAT Act § 38(1), executive order § 17). The deduction percentage is calculated from turnover, applied provisionally during the year and adjusted once the year is closed.

Business Central can apply a fixed non-deductible VAT percentage. It does not calculate the percentage from turnover, change it per year or make the annual adjustment. This app adds those parts on top of Business Central's own non-deductible VAT, following the same pattern Microsoft uses in the Czech localization.

## Status

Specification phase. No code published yet.

- Requirements (Danish): [docs/specs/splitmoms-kravspecifikation.md](docs/specs/splitmoms-kravspecifikation.md)
- Target: Business Central 29, Danish localization
- Build: AL-Go for GitHub with CodeCop, UICop, PerTenantExtensionCop and LinterCop, failing on warnings
- Tests: every rule in the specification has a numbered scenario, tagged `[SCENARIO]`, `[GIVEN]`, `[WHEN]`, `[THEN]`

The intention is to offer the result to Microsoft as a contribution to Business Central.

## Feedback

Comments on the rules are welcome as issues, especially from people who handle partial VAT deduction in practice.

## License

[MIT](LICENSE). Built by [CURABIS](https://curabis.dk).
