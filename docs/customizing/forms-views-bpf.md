# Forms, Views & BPF

## Forms

- **`DGT-CUS-110`**{ #dgt-cus-110 } — **One main form per persona**, not one form with everything on it hidden per role. Use
  role-based form assignment so each audience sees a form built for them. A single form padded
  with tabs no one uses is harder to maintain than two focused ones.
- Use **quick-create** forms for fast inline creation, **quick-view** forms to surface related
  record data read-only, and keep **card** forms lean for views on phones.
- **`DGT-CUS-120`**{ #dgt-cus-120 } — **Logic belongs in code, not click-configured rules, once it's non-trivial.** Simple
  field-level show/hide/require can be a **business rule** (it runs both server- and
  client-side); anything with branching, lookups, or service calls belongs in a
  [TypeScript form script](../coding/clientside/typescript-webresources.md). Register only
  `OnLoad` in the designer and wire the rest in code.

## Views

- Distinguish **system views** (travel with the solution, shared by everyone) from **personal
  views** (a user's own, not part of ALM). Ship the system views the app needs; don't try to
  manage personal views.
- **`DGT-CUS-130`**{ #dgt-cus-130 } — **Keep view columns lean** — every column is a query column. A view with thirty columns is
  slow and unreadable; design for the columns people actually scan.
- Configure **Quick Find** deliberately: its find columns determine global search behavior for
  the table, and too many find columns make search slow.
- Set a sensible **default view** per table, and order views the way users work.

## Business Process Flows (BPF)

- **`DGT-CUS-140`**{ #dgt-cus-140 } — A BPF models a **cross-table process** as ordered stages. Keep stages few and meaningful —
  a stage per database field is process theater, not process guidance.
- A BPF is itself a table (`prfx_..._process`, see
  [Naming Conventions](naming-conventions.md#core-schema)); its instances are rows, and it can
  carry its own plugins. Be aware of the platform limit on active BPFs per table and per
  environment, and retire BPFs you no longer use rather than leaving them deactivated.
- A record can be on **multiple BPFs**; the user switches between them. Don't model parallel
  processes as one branchy flow if they're genuinely separate.

## Modern UI only

**`DGT-CUS-150`**{ #dgt-cus-150 } — Build for the **Unified Interface** model-driven app and use
the modern designers. Don't invest in classic-only form or view features or the classic
designers — they don't carry forward, and Microsoft has deprecated the classic experiences.
