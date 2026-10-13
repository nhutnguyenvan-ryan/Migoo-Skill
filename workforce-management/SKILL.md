---
name: workforce-management
description: >
  [OPS] Vận hành & Quản lý | Tối ưu hóa lịch làm việc, phân bổ ca trực (Shift Allocation) và quản lý năng suất nguồn nhân lực. Từ khóa: WFM, xếp ca, lịch làm việc, shift scheduling, phân bổ nhân sự, năng suất ca làm.
---

<!-- SKILL METADATA (Danh_Sach_Skill_Claude_Chuan_Hoa) -->
<!-- Category: [OPS] Vận hành & Quản lý -->
<!-- Purpose: Tối ưu hóa lịch làm việc, phân bổ ca trực (Shift Allocation) và quản lý năng suất nguồn nhân lực. -->
<!-- Keywords: WFM, xếp ca, lịch làm việc, shift scheduling, phân bổ nhân sự, năng suất ca làm -->

# Workforce Management (Contact Center / BPO)

Approach requests in this domain the way a senior WFM analyst would: ground every number in the data and the math (never eyeball Erlang C), state assumptions explicitly so a number can be audited later, and always connect staffing math back to an operational decision (a schedule, a hiring case, an intraday action) rather than presenting numbers in isolation.

Read `references/wfm-glossary.md` first if there's any ambiguity in the request's terminology (especially "shrinkage" vs "occupancy" vs "utilization" — these are commonly confused, and using them precisely matters for credibility with an ops audience).

## Step 1: Identify which kind of WFM task this is

- **Forecast + staffing/schedule plan** — historical volume/AHT data is provided (or referenced) and the ask is forward-looking: "how many agents do we need," "build next month's schedule," "what's our staffing plan."
- **Real-time / intraday monitoring** — the ask is about how things performed against plan: SL tracking, actual vs. forecast, intraday risk.
- **Shrinkage / adherence analysis** — the ask is about time-use: attendance data, break/adherence logs, "why is our shrinkage so high."
- **Capacity planning / business case** — longer-horizon, leadership-facing: "do we need to hire," "what's the headcount gap for next quarter."
- **Quick what-if / single calculation** — a one-off Erlang question with no deliverable needed.

If genuinely unclear and the data is complex (many queues/channels, long history), a quick clarifying question about the audience and time horizon is worth asking. For a clearly-scoped request, proceed directly.

## Step 2: Choose the output format

Read `references/output-formats.md` for the decision table and structure blueprint — staffing/schedule work usually wants Excel, intraday monitoring usually wants an HTML dashboard, capacity planning business cases usually want a Word/PDF report, and single what-if questions just get answered in chat. Don't default to one format for everything; match it to what the audience will actually do with the deliverable.

## Step 3: Work the data

1. If historical interval-level volume/AHT data is provided, profile it first:
   ```bash
   python scripts/profile_volume.py <path-to-file> --datetime-col <col> --volume-col <col> [--aht-col <col>]
   ```
   This surfaces the day-of-week pattern, intraday shape, peak intervals, and trend read needed for forecasting — don't eyeball the pattern from a raw table.
2. If a forecast is needed, build it using the decomposition method in `references/forecasting-methodology.md` (total volume trend × day-of-week share × intraday share). If the user already supplied a forecast, skip straight to staffing math.
3. Run staffing math through the Erlang C script — never compute Erlang C/B by hand:
   ```bash
   # one-off question
   python scripts/erlang_c.py single --volume <V> --aht <seconds> --interval-minutes <N> \
       --target-sl <0-1> --target-answer-time <seconds> --shrinkage <0-1> [--max-occupancy <0-1>]

   # full interval grid
   python scripts/erlang_c.py batch --input forecast.csv --volume-col volume --aht-col aht \
       --interval-minutes <N> --target-sl <0-1> --target-answer-time <seconds> \
       --shrinkage <0-1> [--max-occupancy <0-1>] --output staffing_plan.csv
   ```
   Read `references/staffing-shrinkage.md` for how to interpret the output, apply shrinkage correctly, use the occupancy guardrail, and turn an interval requirement curve into shift coverage.

## Step 4: Build the deliverable

Follow the structure blueprint in `references/output-formats.md` for whichever format Step 2 selected, and the relevant document-creation skill (`xlsx`, `docx`, `pdf`, or `frontend-design` for an HTML artifact) for the mechanics of producing the file. For dashboards specifically, don't just chart the numbers — surface what's at risk (e.g., which interval is projected to miss SL) and what to do about it, the same insight-then-action structure as general analytical dashboards.

## Principles to carry through every deliverable

- **Show assumptions, not just outputs.** AHT source, shrinkage %, SL target, and occupancy ceiling should always be visible somewhere in the deliverable (an Assumptions tab, a data-notes section, a methodology appendix) — a staffing number without its assumptions can't be trusted or updated later.
- **Don't recommend staffing that implies very high sustained occupancy** without flagging the tradeoff (burnout, AHT inflation, service degradation under variance) — see the glossary.
- **Distinguish root causes.** An SL miss can come from a forecast error, a scheduling/adherence gap, or higher-than-assumed shrinkage — these have different fixes. Don't lump them into one generic "improve performance" recommendation.
- **Be honest about what this skill doesn't fully automate.** Full multi-constraint shift rostering (labor law, bidding, preferences) is a harder optimization problem than this skill solves — give a useful first-pass coverage plan and say so plainly, rather than presenting a greedy coverage suggestion as a finished, constraint-validated roster.
