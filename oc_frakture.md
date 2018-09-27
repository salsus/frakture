select
  left(d.description, 8) launch_date
, substring(substring_index(description, '-', 2), 11) campaign
, d.description
, d.source_code
, program
, round(sum(COALESCE(e.sent)),0) sent
, round(sum(COALESCE(e.opened)),0) opened
, concat(round(sum(COALESCE(e.opened))/ sum(COALESCE(e.sent))*100,2), "%") open_rate
, sum(COALESCE(e.clicked)) clicked
, concat(round(sum(COALESCE(e.clicked)) / sum(COALESCE(e.sent))*100,2), "%") CTR
, COALESCE(t.revenue_first_gifts,0) + COALESCE(t.revenue_one_time,0) revenue
, (t.one_time_gifts + t.first_gifts) responses
, concat(round((t.one_time_gifts + t.first_gifts) / sum(COALESCE(e.clicked))*100,2), "%")  page_completion_rate
, concat(round((t.one_time_gifts + t.first_gifts) / sum(COALESCE(e.sent))*100,4), "%") response_rate
, t.revenue_one_time
, t.one_time_gifts
, t.revenue_first_gifts
, t.first_gifts
, t.revenue_recurring
, t.recurring_gifts
, COALESCE(t.revenue_first_gifts,0) + COALESCE(t.revenue_one_time,0) + COALESCE(t.revenue_recurring,0) total_revenue
, t.total_gifts
 from ocean_conservancy_email_stats_by_source_code e
left join frakture_ocean_conservancy_source_code_dictionary d on e.source_code = d.source_code
left join ocean_conservancy_revenue_by_source_code t on e.source_code = t.clean_source_code
-- where e.source_code like "18L%G%"
group by source_code
order by d.description desc